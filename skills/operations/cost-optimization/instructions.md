# Cost Optimization - Step-by-Step Instructions

## Overview

This document provides detailed, actionable instructions for analyzing and optimizing cloud infrastructure costs. Follow these steps sequentially to achieve comprehensive cost reduction while maintaining performance and reliability.

## Prerequisites

Before starting, ensure you have:

- Read-only access to cloud provider accounts (AWS, Azure, GCP)
- Access to billing and cost management consoles
- Historical billing data (minimum 3 months, ideally 6-12 months)
- Monitoring and metrics access (CloudWatch, Azure Monitor, Cloud Monitoring)
- Understanding of current architecture and services
- Stakeholder alignment on cost optimization goals

## Step 1: Data Collection and Access Setup

### 1.1 Configure Cloud Provider Access

**Objective**: Establish read-only access for cost analysis.

**Actions**:

1. **AWS Access Setup**:
   ```bash
   # Create read-only IAM user for cost analysis
   aws iam create-user --user-name cost-optimization-analyst
   
   # Attach cost explorer and billing read-only policies
   aws iam attach-user-policy \
     --user-name cost-optimization-analyst \
     --policy-arn arn:aws:iam::aws:policy/AWSBillingReadOnlyAccess
   
   aws iam attach-user-policy \
     --user-name cost-optimization-analyst \
     --policy-arn arn:aws:iam::aws:policy/ReadOnlyAccess
   
   # Create access keys
   aws iam create-access-key --user-name cost-optimization-analyst
   ```

2. **Azure Access Setup**:
   ```bash
   # Assign Cost Management Reader role
   az role assignment create \
     --assignee user@domain.com \
     --role "Cost Management Reader" \
     --scope "/subscriptions/{subscription-id}"
   
   # Assign Reader role for resource inventory
   az role assignment create \
     --assignee user@domain.com \
     --role "Reader" \
     --scope "/subscriptions/{subscription-id}"
   ```

3. **GCP Access Setup**:
   ```bash
   # Grant Billing Account Viewer role
   gcloud beta billing accounts add-iam-policy-binding {BILLING_ACCOUNT_ID} \
     --member="user:user@domain.com" \
     --role="roles/billing.viewer"
   
   # Grant Viewer role for resources
   gcloud projects add-iam-policy-binding {PROJECT_ID} \
     --member="user:user@domain.com" \
     --role="roles/viewer"
   ```

### 1.2 Export Historical Billing Data

**Objective**: Collect billing data for analysis.

**Actions**:

1. **AWS Cost and Usage Report**:
   ```bash
   # Enable Cost and Usage Report
   aws cur put-report-definition \
     --report-definition file://cur-definition.json
   ```
   
   ```json
   {
     "ReportName": "cost-optimization-cur",
     "TimeUnit": "HOURLY",
     "Format": "Parquet",
     "Compression": "Parquet",
     "S3Bucket": "cost-reports-bucket",
     "S3Prefix": "cur/",
     "S3Region": "us-east-1",
     "AdditionalSchemaElements": ["RESOURCES"],
     "RefreshClosedReports": true,
     "ReportVersioning": "OVERWRITE_REPORT"
   }
   ```

2. **Azure Cost Export**:
   ```bash
   # Create cost export
   az costmanagement export create \
     --name "cost-optimization-export" \
     --scope "/subscriptions/{subscription-id}" \
     --storage-account-id "/subscriptions/{subscription-id}/resourceGroups/{rg}/providers/Microsoft.Storage/storageAccounts/{account}" \
     --storage-container "cost-exports" \
     --timeframe "MonthToDate" \
     --recurrence "Daily"
   ```

3. **GCP Billing Export**:
   ```bash
   # Enable BigQuery billing export (done via console or API)
   # Export to BigQuery dataset for analysis
   ```

### 1.3 Collect Resource Inventory

**Objective**: Create comprehensive inventory of all cloud resources.

**Actions**:

1. **AWS Resource Inventory**:
   ```python
   # inventory_aws_resources.py
   import boto3
   import csv
   from datetime import datetime
   
   def inventory_ec2_instances(region):
       ec2 = boto3.client('ec2', region_name=region)
       instances = []
       
       response = ec2.describe_instances()
       for reservation in response['Reservations']:
           for instance in reservation['Instances']:
               instances.append({
                   'Region': region,
                   'InstanceId': instance['InstanceId'],
                   'InstanceType': instance['InstanceType'],
                   'State': instance['State']['Name'],
                   'LaunchTime': instance['LaunchTime'],
                   'Tags': {tag['Key']: tag['Value'] for tag in instance.get('Tags', [])}
               })
       
       return instances
   
   def inventory_rds_instances(region):
       rds = boto3.client('rds', region_name=region)
       instances = []
       
       response = rds.describe_db_instances()
       for db in response['DBInstances']:
           instances.append({
               'Region': region,
               'DBInstanceIdentifier': db['DBInstanceIdentifier'],
               'DBInstanceClass': db['DBInstanceClass'],
               'Engine': db['Engine'],
               'EngineVersion': db['EngineVersion'],
               'AllocatedStorage': db['AllocatedStorage'],
               'MultiAZ': db['MultiAZ']
           })
       
       return instances
   
   # Inventory all regions
   regions = ['us-east-1', 'us-west-2', 'eu-west-1']  # Add all regions
   
   all_ec2 = []
   all_rds = []
   
   for region in regions:
       all_ec2.extend(inventory_ec2_instances(region))
       all_rds.extend(inventory_rds_instances(region))
   
   # Export to CSV
   with open('aws_ec2_inventory.csv', 'w', newline='') as f:
       if all_ec2:
           writer = csv.DictWriter(f, fieldnames=all_ec2[0].keys())
           writer.writeheader()
           writer.writerows(all_ec2)
   
   print(f"Inventoried {len(all_ec2)} EC2 instances and {len(all_rds)} RDS instances")
   ```

2. **Collect Performance Metrics**:
   ```python
   # collect_metrics.py
   import boto3
   from datetime import datetime, timedelta
   
   cloudwatch = boto3.client('cloudwatch')
   
   def get_instance_cpu_utilization(instance_id, days=30):
       end_time = datetime.now()
       start_time = end_time - timedelta(days=days)
       
       response = cloudwatch.get_metric_statistics(
           Namespace='AWS/EC2',
           MetricName='CPUUtilization',
           Dimensions=[{'Name': 'InstanceId', 'Value': instance_id}],
           StartTime=start_time,
           EndTime=end_time,
           Period=3600,  # 1 hour
           Statistics=['Average', 'Maximum']
       )
       
       return response['Datapoints']
   
   # Collect for all instances
   # ... (implementation)
   ```

**Deliverable**: Resource inventory spreadsheets, billing data exports, performance metrics access.

---

## Step 2: Current State Cost Analysis

### 2.1 Analyze Total Spending Trends

**Objective**: Understand overall cost trends and patterns.

**Actions**:

1. **Calculate Monthly Costs**:
   ```python
   # analyze_costs.py
   import pandas as pd
   import matplotlib.pyplot as plt
   
   # Load billing data
   df = pd.read_csv('billing_data.csv')
   df['Date'] = pd.to_datetime(df['Date'])
   
   # Group by month
   monthly_costs = df.groupby(df['Date'].dt.to_period('M'))['Cost'].sum()
   
   # Calculate growth rate
   growth_rate = monthly_costs.pct_change().mean() * 100
   
   print(f"Average monthly growth rate: {growth_rate:.2f}%")
   
   # Plot trend
   monthly_costs.plot(kind='line', title='Monthly Cloud Costs', ylabel='Cost ($)')
   plt.savefig('cost_trend.png')
   ```

2. **Identify Seasonal Patterns**:
   - Analyze costs by day of week
   - Identify monthly patterns
   - Note any anomalies or spikes
   - Document seasonal business events

### 2.2 Break Down Costs by Service

**Objective**: Identify which services drive the most cost.

**Actions**:

1. **Service Cost Breakdown**:
   ```python
   # Service breakdown
   service_costs = df.groupby('Service')['Cost'].sum().sort_values(ascending=False)
   
   # Top 10 services
   top_services = service_costs.head(10)
   
   print("Top 10 Cost Drivers:")
   for service, cost in top_services.items():
       percentage = (cost / service_costs.sum()) * 100
       print(f"{service}: ${cost:,.2f} ({percentage:.1f}%)")
   
   # Pie chart
   top_services.plot(kind='pie', title='Cost by Service', autopct='%1.1f%%')
   plt.savefig('service_breakdown.png')
   ```

2. **Environment Segmentation**:
   ```python
   # Costs by environment (requires proper tagging)
   env_costs = df.groupby('Environment')['Cost'].sum()
   
   print("\nCosts by Environment:")
   for env, cost in env_costs.items():
       percentage = (cost / env_costs.sum()) * 100
       print(f"{env}: ${cost:,.2f} ({percentage:.1f}%)")
   ```

### 2.3 Calculate Unit Economics

**Objective**: Understand cost per business metric.

**Actions**:

1. **Cost per Customer/Transaction**:
   ```python
   # Calculate unit economics
   total_cost = df['Cost'].sum()
   total_customers = 50000  # From business metrics
   total_transactions = 1000000  # From business metrics
   
   cost_per_customer = total_cost / total_customers
   cost_per_transaction = total_cost / total_transactions
   
   print(f"Cost per customer: ${cost_per_customer:.2f}")
   print(f"Cost per transaction: ${cost_per_transaction:.4f}")
   ```

### 2.4 Benchmark Against Industry Standards

**Objective**: Compare costs to similar workloads.

**Actions**:

1. **Research Industry Benchmarks**:
   - SaaS: $0.50-$2.00 per user per month for infrastructure
   - E-commerce: 2-5% of revenue for infrastructure
   - Gaming: $0.10-$0.50 per DAU (daily active user)

2. **Compare and Document**:
   ```markdown
   ## Benchmark Comparison
   
   Our Metrics:
   - Cost per customer: $2.40/month
   - Infrastructure as % of revenue: 8%
   
   Industry Benchmark:
   - Cost per customer: $1.50/month (target)
   - Infrastructure as % of revenue: 4% (target)
   
   Gap Analysis:
   - 60% higher cost per customer
   - 100% higher infrastructure percentage
   - Opportunity: $45K/month savings to reach benchmark
   ```

**Deliverable**: Cost analysis dashboard, spending trend charts, service breakdown, unit economics, benchmarking report.

---

## Step 3: Resource Utilization Analysis

### 3.1 Analyze Compute Utilization

**Objective**: Identify underutilized and over-provisioned compute resources.

**Actions**:

1. **Calculate Average CPU Utilization**:
   ```python
   # analyze_utilization.py
   import boto3
   from datetime import datetime, timedelta
   import pandas as pd
   
   cloudwatch = boto3.client('cloudwatch')
   ec2 = boto3.client('ec2')
   
   def get_instance_utilization(instance_id, days=30):
       end_time = datetime.now()
       start_time = end_time - timedelta(days=days)
       
       # Get CPU metrics
       cpu_response = cloudwatch.get_metric_statistics(
           Namespace='AWS/EC2',
           MetricName='CPUUtilization',
           Dimensions=[{'Name': 'InstanceId', 'Value': instance_id}],
           StartTime=start_time,
           EndTime=end_time,
           Period=3600,
           Statistics=['Average', 'Maximum']
       )
       
       if not cpu_response['Datapoints']:
           return None
       
       avg_cpu = sum(dp['Average'] for dp in cpu_response['Datapoints']) / len(cpu_response['Datapoints'])
       max_cpu = max(dp['Maximum'] for dp in cpu_response['Datapoints'])
       
       return {
           'InstanceId': instance_id,
           'AvgCPU': avg_cpu,
           'MaxCPU': max_cpu
       }
   
   # Get all running instances
   instances_response = ec2.describe_instances(
       Filters=[{'Name': 'instance-state-name', 'Values': ['running']}]
   )
   
   utilization_data = []
   for reservation in instances_response['Reservations']:
       for instance in reservation['Instances']:
           util = get_instance_utilization(instance['InstanceId'])
           if util:
               util['InstanceType'] = instance['InstanceType']
               util['Tags'] = {tag['Key']: tag['Value'] for tag in instance.get('Tags', [])}
               utilization_data.append(util)
   
   # Create DataFrame
   df = pd.DataFrame(utilization_data)
   
   # Categorize instances
   df['Category'] = df['AvgCPU'].apply(lambda x: 
       'Idle' if x < 5 else
       'Underutilized' if x < 30 else
       'Optimal' if x < 70 else
       'High'
   )
   
   # Summary
   print("\nUtilization Summary:")
   print(df.groupby('Category').size())
   print("\nIdle Instances (< 5% CPU):")
   print(df[df['Category'] == 'Idle'][['InstanceId', 'InstanceType', 'AvgCPU']])
   
   # Export
   df.to_csv('instance_utilization.csv', index=False)
   ```

2. **Identify Idle Resources**:
   ```python
   # Find resources with < 5% CPU for 7+ days
   idle_threshold = 5
   idle_instances = df[df['AvgCPU'] < idle_threshold]
   
   print(f"\nFound {len(idle_instances)} idle instances")
   print("Estimated monthly cost: $X,XXX")  # Calculate from pricing
   ```

### 3.2 Analyze Storage Utilization

**Objective**: Identify over-provisioned and unused storage.

**Actions**:

1. **EBS Volume Analysis**:
   ```python
   # analyze_storage.py
   import boto3
   
   ec2 = boto3.client('ec2')
   
   # Get all volumes
   volumes = ec2.describe_volumes()['Volumes']
   
   unattached_volumes = []
   over_provisioned_iops = []
   
   for volume in volumes:
       # Check if attached
       if len(volume['Attachments']) == 0:
           unattached_volumes.append({
               'VolumeId': volume['VolumeId'],
               'Size': volume['Size'],
               'VolumeType': volume['VolumeType'],
               'CreateTime': volume['CreateTime']
           })
       
       # Check IOPS provisioning (for io1/io2)
       if volume['VolumeType'] in ['io1', 'io2']:
           # Get actual IOPS usage from CloudWatch
           # Compare to provisioned IOPS
           # Flag if usage < 50% of provisioned
           pass
   
   print(f"\nFound {len(unattached_volumes)} unattached volumes")
   print(f"Estimated monthly waste: $X,XXX")
   ```

2. **S3 Storage Analysis**:
   ```python
   # analyze_s3.py
   import boto3
   
   s3 = boto3.client('s3')
   cloudwatch = boto3.client('cloudwatch')
   
   buckets = s3.list_buckets()['Buckets']
   
   for bucket in buckets:
       bucket_name = bucket['Name']
       
       # Get storage metrics
       response = cloudwatch.get_metric_statistics(
           Namespace='AWS/S3',
           MetricName='BucketSizeBytes',
           Dimensions=[
               {'Name': 'BucketName', 'Value': bucket_name},
               {'Name': 'StorageType', 'Value': 'StandardStorage'}
           ],
           StartTime=datetime.now() - timedelta(days=1),
           EndTime=datetime.now(),
           Period=86400,
           Statistics=['Average']
       )
       
       if response['Datapoints']:
           size_bytes = response['Datapoints'][0]['Average']
           size_gb = size_bytes / (1024**3)
           
           # Check for lifecycle policies
           try:
               lifecycle = s3.get_bucket_lifecycle_configuration(Bucket=bucket_name)
               has_lifecycle = True
           except:
               has_lifecycle = False
           
           if not has_lifecycle and size_gb > 100:
               print(f"Bucket {bucket_name}: {size_gb:.2f} GB, no lifecycle policy")
   ```

### 3.3 Analyze Database Utilization

**Objective**: Right-size database instances.

**Actions**:

1. **RDS Instance Analysis**:
   ```python
   # analyze_rds.py
   import boto3
   from datetime import datetime, timedelta
   
   rds = boto3.client('rds')
   cloudwatch = boto3.client('cloudwatch')
   
   instances = rds.describe_db_instances()['DBInstances']
   
   for instance in instances:
       db_id = instance['DBInstanceIdentifier']
       db_class = instance['DBInstanceClass']
       
       # Get CPU utilization
       cpu_response = cloudwatch.get_metric_statistics(
           Namespace='AWS/RDS',
           MetricName='CPUUtilization',
           Dimensions=[{'Name': 'DBInstanceIdentifier', 'Value': db_id}],
           StartTime=datetime.now() - timedelta(days=30),
           EndTime=datetime.now(),
           Period=3600,
           Statistics=['Average', 'Maximum']
       )
       
       # Get connection count
       conn_response = cloudwatch.get_metric_statistics(
           Namespace='AWS/RDS',
           MetricName='DatabaseConnections',
           Dimensions=[{'Name': 'DBInstanceIdentifier', 'Value': db_id}],
           StartTime=datetime.now() - timedelta(days=30),
           EndTime=datetime.now(),
           Period=3600,
           Statistics=['Average', 'Maximum']
       )
       
       if cpu_response['Datapoints']:
           avg_cpu = sum(dp['Average'] for dp in cpu_response['Datapoints']) / len(cpu_response['Datapoints'])
           max_cpu = max(dp['Maximum'] for dp in cpu_response['Datapoints'])
           
           print(f"\n{db_id} ({db_class}):")
           print(f"  Avg CPU: {avg_cpu:.2f}%")
           print(f"  Max CPU: {max_cpu:.2f}%")
           
           if avg_cpu < 30:
               print(f"  Recommendation: Consider downsizing")
   ```

**Deliverable**: Resource utilization report, idle resource list, over-provisioned resource list, utilization heatmaps.

---

## Step 4: Right-Sizing Recommendations

### 4.1 Calculate Recommended Instance Sizes

**Objective**: Determine optimal instance types based on actual usage.

**Actions**:

1. **EC2 Right-Sizing Logic**:
   ```python
   # rightsizing.py
   import pandas as pd
   
   def recommend_instance_type(current_type, avg_cpu, max_cpu, avg_memory=None):
       # Instance family mapping (simplified)
       instance_families = {
           'm5': ['m5.large', 'm5.xlarge', 'm5.2xlarge', 'm5.4xlarge'],
           'c5': ['c5.large', 'c5.xlarge', 'c5.2xlarge', 'c5.4xlarge'],
           't3': ['t3.small', 't3.medium', 't3.large', 't3.xlarge']
       }
       
       # Get current family
       family = current_type.split('.')[0]
       
       # Decision logic
       if avg_cpu < 20 and max_cpu < 50:
           # Consider burstable instance
           return 't3.' + current_type.split('.')[1], "Burstable instance for low utilization"
       
       elif avg_cpu < 40:
           # Downsize within same family
           sizes = instance_families.get(family, [])
           current_idx = sizes.index(current_type) if current_type in sizes else -1
           if current_idx > 0:
               return sizes[current_idx - 1], "Downsize due to low utilization"
       
       elif avg_cpu > 70:
           # Upsize within same family
           sizes = instance_families.get(family, [])
           current_idx = sizes.index(current_type) if current_type in sizes else -1
           if current_idx < len(sizes) - 1:
               return sizes[current_idx + 1], "Upsize due to high utilization"
       
       return current_type, "Current size is optimal"
   
   # Load utilization data
   df = pd.read_csv('instance_utilization.csv')
   
   # Generate recommendations
   recommendations = []
   for _, row in df.iterrows():
       recommended_type, reason = recommend_instance_type(
           row['InstanceType'],
           row['AvgCPU'],
           row['MaxCPU']
       )
       
       if recommended_type != row['InstanceType']:
           # Calculate savings (simplified)
           current_cost = get_instance_cost(row['InstanceType'])  # Implement pricing lookup
           recommended_cost = get_instance_cost(recommended_type)
           monthly_savings = (current_cost - recommended_cost) * 730  # hours per month
           
           recommendations.append({
               'InstanceId': row['InstanceId'],
               'CurrentType': row['InstanceType'],
               'RecommendedType': recommended_type,
               'AvgCPU': row['AvgCPU'],
               'MaxCPU': row['MaxCPU'],
               'Reason': reason,
               'MonthlySavings': monthly_savings
           })
   
   # Sort by savings
   recommendations_df = pd.DataFrame(recommendations)
   recommendations_df = recommendations_df.sort_values('MonthlySavings', ascending=False)
   
   print(f"\nTotal potential savings: ${recommendations_df['MonthlySavings'].sum():,.2f}/month")
   recommendations_df.to_csv('rightsizing_recommendations.csv', index=False)
   ```

### 4.2 Assess Risk and Performance Impact

**Objective**: Evaluate risk of each right-sizing recommendation.

**Actions**:

1. **Risk Assessment Matrix**:
   ```python
   def assess_risk(row):
       risk_score = 0
       risk_factors = []
       
       # Factor 1: Utilization headroom
       if row['MaxCPU'] > 80:
           risk_score += 3
           risk_factors.append("High peak utilization")
       elif row['MaxCPU'] > 60:
           risk_score += 2
           risk_factors.append("Moderate peak utilization")
       
       # Factor 2: Size of downgrade
       current_size = row['CurrentType'].split('.')[1]
       recommended_size = row['RecommendedType'].split('.')[1]
       size_order = ['nano', 'micro', 'small', 'medium', 'large', 'xlarge', '2xlarge', '4xlarge']
       
       if current_size in size_order and recommended_size in size_order:
           size_diff = size_order.index(current_size) - size_order.index(recommended_size)
           if size_diff > 2:
               risk_score += 3
               risk_factors.append("Large downsize")
           elif size_diff > 1:
               risk_score += 1
               risk_factors.append("Moderate downsize")
       
       # Factor 3: Environment
       env = row.get('Environment', 'unknown')
       if env == 'production':
           risk_score += 2
           risk_factors.append("Production environment")
       
       # Categorize risk
       if risk_score >= 6:
           risk_level = 'High'
       elif risk_score >= 3:
           risk_level = 'Medium'
       else:
           risk_level = 'Low'
       
       return risk_level, ', '.join(risk_factors)
   
   # Apply to recommendations
   recommendations_df['RiskLevel'], recommendations_df['RiskFactors'] = zip(
       *recommendations_df.apply(assess_risk, axis=1)
   )
   ```

### 4.3 Prioritize Recommendations

**Objective**: Rank recommendations by impact and effort.

**Actions**:

1. **Priority Scoring**:
   ```python
   def calculate_priority_score(row):
       # Impact: Monthly savings
       impact_score = min(row['MonthlySavings'] / 1000, 10)  # Cap at 10
       
       # Effort: Inversely related to risk
       risk_to_effort = {'Low': 1, 'Medium': 3, 'High': 5}
       effort_score = risk_to_effort.get(row['RiskLevel'], 3)
       
       # Priority = Impact / Effort (higher is better)
       priority_score = impact_score / effort_score
       
       return priority_score
   
   recommendations_df['PriorityScore'] = recommendations_df.apply(calculate_priority_score, axis=1)
   recommendations_df = recommendations_df.sort_values('PriorityScore', ascending=False)
   
   # Categorize
   recommendations_df['Priority'] = pd.cut(
       recommendations_df['PriorityScore'],
       bins=[0, 1, 3, float('inf')],
       labels=['Low', 'Medium', 'High']
   )
   
   print("\nRecommendations by Priority:")
   print(recommendations_df.groupby('Priority').agg({
       'InstanceId': 'count',
       'MonthlySavings': 'sum'
   }))
   ```

**Deliverable**: Right-sizing recommendation spreadsheet, savings estimates, risk assessment matrix, priority ranking.

---

## Step 5: Reserved Capacity and Commitment Analysis

### 5.1 Analyze Current Reserved Instance Portfolio

**Objective**: Understand existing commitments and utilization.

**Actions**:

1. **AWS Reserved Instance Analysis**:
   ```python
   # analyze_reservations.py
   import boto3
   from datetime import datetime, timedelta
   
   ec2 = boto3.client('ec2')
   ce = boto3.client('ce')
   
   # Get active reserved instances
   reservations = ec2.describe_reserved_instances(
       Filters=[{'Name': 'state', 'Values': ['active']}]
   )['ReservedInstances']
   
   print("Active Reserved Instances:")
   for ri in reservations:
       print(f"\n{ri['ReservedInstancesId']}:")
       print(f"  Type: {ri['InstanceType']}")
       print(f"  Count: {ri['InstanceCount']}")
       print(f"  Start: {ri['Start']}")
       print(f"  End: {ri['End']}")
       print(f"  Offering: {ri['OfferingType']}")
       
       # Check if expiring soon (within 90 days)
       days_until_expiry = (ri['End'].replace(tzinfo=None) - datetime.now()).days
       if days_until_expiry < 90:
           print(f"  WARNING: Expires in {days_until_expiry} days")
   
   # Get RI utilization
   utilization = ce.get_reservation_utilization(
       TimePeriod={
           'Start': (datetime.now() - timedelta(days=30)).strftime('%Y-%m-%d'),
           'End': datetime.now().strftime('%Y-%m-%d')
       },
       Granularity='MONTHLY'
   )
   
   for period in utilization['UtilizationsByTime']:
       util = period['Total']
       print(f"\nRI Utilization ({period['TimePeriod']['Start']}):")
       print(f"  Utilization: {util['UtilizationPercentage']}%")
       print(f"  Purchased Hours: {util['PurchasedHours']}")
       print(f"  Used Hours: {util['UsedHours']}")
       print(f"  Unused Hours: {util['UnusedHours']}")
   ```

### 5.2 Identify Steady-State Workloads

**Objective**: Find workloads suitable for reserved capacity.

**Actions**:

1. **Analyze Instance Stability**:
   ```python
   # identify_steady_workloads.py
   import pandas as pd
   from datetime import datetime, timedelta
   
   # Load historical instance data (from inventory over time)
   df = pd.read_csv('instance_history.csv')
   df['Date'] = pd.to_datetime(df['Date'])
   
   # Group by instance type and count daily
   daily_counts = df.groupby([df['Date'].dt.date, 'InstanceType']).size().reset_index(name='Count')
   
   # Calculate stability metrics
   stability = daily_counts.groupby('InstanceType').agg({
       'Count': ['mean', 'std', 'min', 'max']
   })
   
   # Calculate coefficient of variation (lower = more stable)
   stability['CV'] = stability[('Count', 'std')] / stability[('Count', 'mean')]
   
   # Identify stable workloads (CV < 0.2)
   stable_workloads = stability[stability['CV'] < 0.2].sort_values(('Count', 'mean'), ascending=False)
   
   print("Stable Workloads (suitable for RIs):")
   print(stable_workloads)
   ```

### 5.3 Calculate Optimal Reserved Instance Coverage

**Objective**: Determine how many reserved instances to purchase.

**Actions**:

1. **Coverage Calculation**:
   ```python
   # calculate_ri_coverage.py
   
   def calculate_optimal_ri_coverage(instance_type, usage_data):
       # Get baseline usage (minimum over analysis period)
       baseline = usage_data['Count'].quantile(0.1)  # 10th percentile
       
       # Get average usage
       average = usage_data['Count'].mean()
       
       # Recommended coverage: 60-80% of average, not exceeding baseline
       recommended_coverage = min(
           int(average * 0.7),  # 70% of average
           int(baseline)  # Don't exceed baseline
       )
       
       return recommended_coverage
   
   # Calculate for each instance type
   ri_recommendations = []
   for instance_type in stable_workloads.index:
       usage = daily_counts[daily_counts['InstanceType'] == instance_type]
       coverage = calculate_optimal_ri_coverage(instance_type, usage)
       
       if coverage > 0:
           # Calculate savings
           on_demand_cost = get_on_demand_cost(instance_type)  # Implement
           ri_cost = get_ri_cost(instance_type, '1yr', 'partial')  # Implement
           monthly_savings = (on_demand_cost - ri_cost) * coverage * 730
           
           ri_recommendations.append({
               'InstanceType': instance_type,
               'RecommendedCount': coverage,
               'Term': '1yr',
               'PaymentOption': 'Partial Upfront',
               'MonthlySavings': monthly_savings,
               'UpfrontCost': ri_cost * coverage * 365 * 0.5  # Partial upfront
           })
   
   ri_df = pd.DataFrame(ri_recommendations)
   ri_df = ri_df.sort_values('MonthlySavings', ascending=False)
   
   print(f"\nTotal RI Recommendations: {ri_df['RecommendedCount'].sum()} instances")
   print(f"Total Monthly Savings: ${ri_df['MonthlySavings'].sum():,.2f}")
   print(f"Total Upfront Cost: ${ri_df['UpfrontCost'].sum():,.2f}")
   
   ri_df.to_csv('ri_recommendations.csv', index=False)
   ```

### 5.4 Compare Reserved Instances vs Savings Plans

**Objective**: Determine best commitment type.

**Actions**:

1. **Savings Plan Analysis**:
   ```python
   # compare_commitments.py
   
   def compare_ri_vs_savings_plan(compute_spend_per_hour):
       # 1-year commitment comparison
       
       # Reserved Instance (specific instance type)
       ri_discount = 0.40  # 40% discount
       ri_flexibility = 'Low'  # Locked to instance family
       
       # Compute Savings Plan (flexible)
       sp_discount = 0.35  # 35% discount (slightly less than RI)
       sp_flexibility = 'High'  # Any instance type, any region
       
       # Calculate costs
       on_demand_annual = compute_spend_per_hour * 8760
       ri_annual = on_demand_annual * (1 - ri_discount)
       sp_annual = on_demand_annual * (1 - sp_discount)
       
       return {
           'OnDemand': on_demand_annual,
           'ReservedInstance': {
               'Cost': ri_annual,
               'Savings': on_demand_annual - ri_annual,
               'Discount': f"{ri_discount*100}%",
               'Flexibility': ri_flexibility
           },
           'SavingsPlan': {
               'Cost': sp_annual,
               'Savings': on_demand_annual - sp_annual,
               'Discount': f"{sp_discount*100}%",
               'Flexibility': sp_flexibility
           }
       }
   
   # Recommendation logic
   def recommend_commitment_type(workload_characteristics):
       if workload_characteristics['instance_type_changes_frequently']:
           return 'Savings Plan', 'Workload requires flexibility'
       elif workload_characteristics['multi_region']:
           return 'Savings Plan', 'Multi-region deployment'
       elif workload_characteristics['very_stable']:
           return 'Reserved Instance', 'Stable workload, maximize savings'
       else:
           return 'Savings Plan', 'Default recommendation for flexibility'
   ```

**Deliverable**: Reserved capacity recommendation report, savings plan analysis, ROI calculations, coverage gap analysis.

---

## Step 6: Architecture and Service Optimization

### 6.1 Evaluate Serverless Migration Opportunities

**Objective**: Identify workloads suitable for serverless.

**Actions**:

1. **Serverless Suitability Assessment**:
   ```python
   # assess_serverless.py
   
   def assess_serverless_suitability(workload):
       score = 0
       reasons = []
       
       # Factor 1: Event-driven
       if workload.get('event_driven', False):
           score += 3
           reasons.append("Event-driven architecture")
       
       # Factor 2: Variable traffic
       if workload.get('traffic_variability', 0) > 0.5:  # High variability
           score += 3
           reasons.append("Variable traffic patterns")
       
       # Factor 3: Short execution time
       if workload.get('avg_execution_time', 0) < 300:  # < 5 minutes
           score += 2
           reasons.append("Short execution time")
       
       # Factor 4: Stateless
       if workload.get('stateless', False):
           score += 2
           reasons.append("Stateless processing")
       
       # Disqualifiers
       if workload.get('avg_execution_time', 0) > 900:  # > 15 minutes
           score = 0
           reasons = ["Execution time too long for Lambda"]
       
       if workload.get('requires_persistent_connections', False):
           score = 0
           reasons = ["Requires persistent connections"]
       
       # Recommendation
       if score >= 7:
           recommendation = 'Highly Recommended'
       elif score >= 4:
           recommendation = 'Consider'
       else:
           recommendation = 'Not Recommended'
       
       return {
           'Score': score,
           'Recommendation': recommendation,
           'Reasons': reasons
       }
   
   # Example workload analysis
   workloads = [
       {
           'name': 'Image Processing',
           'event_driven': True,
           'traffic_variability': 0.8,
           'avg_execution_time': 45,
           'stateless': True,
           'requires_persistent_connections': False
       },
       {
           'name': 'API Backend',
           'event_driven': True,
           'traffic_variability': 0.3,
           'avg_execution_time': 100,
           'stateless': True,
           'requires_persistent_connections': False
       }
   ]
   
   for workload in workloads:
       result = assess_serverless_suitability(workload)
       print(f"\n{workload['name']}:")
       print(f"  Recommendation: {result['Recommendation']}")
       print(f"  Reasons: {', '.join(result['Reasons'])}")
   ```

### 6.2 Assess Containerization Benefits

**Objective**: Evaluate container migration for better resource utilization.

**Actions**:

1. **Container ROI Analysis**:
   ```markdown
   ## Containerization ROI Analysis
   
   ### Current State (VMs)
   - 100 EC2 instances (m5.xlarge)
   - Average utilization: 30%
   - Monthly cost: $14,600
   
   ### Proposed State (ECS/EKS)
   - 30 EC2 instances (m5.xlarge) for ECS cluster
   - Average utilization: 70% (better bin packing)
   - Monthly cost: $4,380 (instances) + $219 (EKS control plane) = $4,599
   
   ### Savings
   - Monthly: $10,001 (68% reduction)
   - Annual: $120,012
   
   ### Implementation Effort
   - Containerize applications: 4-6 weeks
   - Set up ECS/EKS: 1-2 weeks
   - Migration and testing: 2-3 weeks
   - Total: 7-11 weeks
   
   ### ROI
   - Payback period: 1 month
   - 3-year savings: $360,036
   ```

### 6.3 Identify Caching Opportunities

**Objective**: Reduce load and costs through caching.

**Actions**:

1. **Cache Analysis**:
   ```python
   # analyze_cache_opportunities.py
   
   def analyze_api_patterns(api_logs):
       # Analyze API request patterns
       df = pd.read_csv(api_logs)
       
       # Calculate request frequency by endpoint
       endpoint_freq = df.groupby('endpoint').size().sort_values(ascending=False)
       
       # Calculate response time
       endpoint_latency = df.groupby('endpoint')['response_time'].mean()
       
       # Identify cacheable endpoints (high frequency, slow response)
       cacheable = endpoint_freq[endpoint_freq > 1000].index  # > 1000 requests
       slow_endpoints = endpoint_latency[endpoint_latency > 500].index  # > 500ms
       
       cache_candidates = list(set(cacheable) & set(slow_endpoints))
       
       print("Cache Candidates:")
       for endpoint in cache_candidates:
           freq = endpoint_freq[endpoint]
           latency = endpoint_latency[endpoint]
           
           # Estimate savings (reduced database load)
           db_cost_per_query = 0.001  # $0.001 per query
           cache_hit_rate = 0.8  # Assume 80% hit rate
           monthly_savings = freq * 30 * db_cost_per_query * cache_hit_rate
           
           print(f"\n{endpoint}:")
           print(f"  Frequency: {freq} requests/day")
           print(f"  Latency: {latency:.0f}ms")
           print(f"  Estimated savings: ${monthly_savings:.2f}/month")
   ```

**Deliverable**: Architecture optimization proposals, service migration recommendations, cost-benefit analysis, implementation complexity assessment.

---

## Step 7: Storage Optimization

### 7.1 Implement S3 Lifecycle Policies

**Objective**: Automatically tier storage to reduce costs.

**Actions**:

1. **Analyze S3 Access Patterns**:
   ```python
   # analyze_s3_access.py
   import boto3
   from datetime import datetime, timedelta
   
   s3 = boto3.client('s3')
   
   def analyze_object_age_and_access(bucket_name):
       # Enable S3 Storage Lens or use S3 Inventory
       # Analyze object age and last access time
       
       objects = s3.list_objects_v2(Bucket=bucket_name)
       
       age_distribution = {
           '0-30 days': 0,
           '30-90 days': 0,
           '90-365 days': 0,
           '1-7 years': 0,
           '7+ years': 0
       }
       
       for obj in objects.get('Contents', []):
           age_days = (datetime.now(obj['LastModified'].tzinfo) - obj['LastModified']).days
           
           if age_days < 30:
               age_distribution['0-30 days'] += obj['Size']
           elif age_days < 90:
               age_distribution['30-90 days'] += obj['Size']
           elif age_days < 365:
               age_distribution['90-365 days'] += obj['Size']
           elif age_days < 2555:  # 7 years
               age_distribution['1-7 years'] += obj['Size']
           else:
               age_distribution['7+ years'] += obj['Size']
       
       return age_distribution
   ```

2. **Create Lifecycle Policy**:
   ```python
   # create_lifecycle_policy.py
   import boto3
   
   s3 = boto3.client('s3')
   
   lifecycle_policy = {
       'Rules': [
           {
               'Id': 'Transition to IA after 30 days',
               'Status': 'Enabled',
               'Transitions': [
                   {
                       'Days': 30,
                       'StorageClass': 'STANDARD_IA'
                   },
                   {
                       'Days': 90,
                       'StorageClass': 'GLACIER_IR'
                   },
                   {
                       'Days': 365,
                       'StorageClass': 'GLACIER'
                   },
                   {
                       'Days': 2555,  # 7 years
                       'StorageClass': 'DEEP_ARCHIVE'
                   }
               ]
           },
           {
               'Id': 'Delete old logs after 90 days',
               'Status': 'Enabled',
               'Filter': {
                   'Prefix': 'logs/'
               },
               'Expiration': {
                   'Days': 90
               }
           }
       ]
   }
   
   s3.put_bucket_lifecycle_configuration(
       Bucket='my-bucket',
       LifecycleConfiguration=lifecycle_policy
   )
   ```

### 7.2 Optimize Backup Retention

**Objective**: Reduce backup storage costs.

**Actions**:

1. **Review Backup Policies**:
   ```markdown
   ## Backup Retention Analysis
   
   ### Current Policy
   - Daily backups: Retained for 90 days
   - Weekly backups: Retained for 1 year
   - Monthly backups: Retained for 7 years
   
   ### Compliance Requirements
   - Regulatory: 7 years retention
   - Recovery needs: 30 days of daily backups
   
   ### Optimized Policy
   - Daily backups: Retain 30 days (Standard)
   - Weekly backups: Retain 12 weeks (Standard)
   - Monthly backups: Retain 12 months (Standard)
   - Annual backups: Retain 7 years (Glacier Deep Archive)
   
   ### Savings
   - Current: 90 daily + 52 weekly + 84 monthly = 226 backup copies
   - Optimized: 30 daily + 12 weekly + 12 monthly + 7 annual = 61 backup copies
   - Reduction: 73%
   - Estimated savings: $X,XXX/month
   ```

### 7.3 Delete Orphaned Resources

**Objective**: Clean up unused storage resources.

**Actions**:

1. **Identify Orphaned EBS Snapshots**:
   ```python
   # find_orphaned_snapshots.py
   import boto3
   
   ec2 = boto3.client('ec2')
   
   # Get all snapshots owned by account
   snapshots = ec2.describe_snapshots(OwnerIds=['self'])['Snapshots']
   
   # Get all volumes
   volumes = ec2.describe_volumes()['Volumes']
   volume_ids = {vol['VolumeId'] for vol in volumes}
   
   # Get all AMIs
   images = ec2.describe_images(Owners=['self'])['Images']
   ami_snapshot_ids = set()
   for image in images:
       for bdm in image.get('BlockDeviceMappings', []):
           if 'Ebs' in bdm:
               ami_snapshot_ids.add(bdm['Ebs']['SnapshotId'])
   
   # Find orphaned snapshots
   orphaned_snapshots = []
   for snapshot in snapshots:
       volume_id = snapshot.get('VolumeId')
       snapshot_id = snapshot['SnapshotId']
       
       # Check if volume still exists or snapshot is used by AMI
       if volume_id not in volume_ids and snapshot_id not in ami_snapshot_ids:
           orphaned_snapshots.append({
               'SnapshotId': snapshot_id,
               'VolumeId': volume_id,
               'Size': snapshot['VolumeSize'],
               'StartTime': snapshot['StartTime'],
               'Description': snapshot.get('Description', '')
           })
   
   print(f"Found {len(orphaned_snapshots)} orphaned snapshots")
   total_size = sum(snap['Size'] for snap in orphaned_snapshots)
   estimated_cost = total_size * 0.05  # $0.05 per GB-month
   print(f"Total size: {total_size} GB")
   print(f"Estimated monthly cost: ${estimated_cost:.2f}")
   ```

**Deliverable**: Storage optimization report, lifecycle policy recommendations, cleanup candidate list, projected storage cost savings.

---

## Step 8: Network and Data Transfer Optimization

### 8.1 Analyze Data Transfer Costs

**Objective**: Understand and reduce data transfer expenses.

**Actions**:

1. **Data Transfer Analysis**:
   ```python
   # analyze_data_transfer.py
   import boto3
   import pandas as pd
   
   ce = boto3.client('ce')
   
   # Get data transfer costs
   response = ce.get_cost_and_usage(
       TimePeriod={
           'Start': '2024-01-01',
           'End': '2024-02-01'
       },
       Granularity='MONTHLY',
       Metrics=['UnblendedCost'],
       Filter={
           'Dimensions': {
               'Key': 'USAGE_TYPE_GROUP',
               'Values': ['EC2: Data Transfer']
           }
       },
       GroupBy=[
           {'Type': 'DIMENSION', 'Key': 'USAGE_TYPE'}
       ]
   )
   
   # Analyze by type
   transfer_costs = {}
   for result in response['ResultsByTime']:
       for group in result['Groups']:
           usage_type = group['Keys'][0]
           cost = float(group['Metrics']['UnblendedCost']['Amount'])
           transfer_costs[usage_type] = cost
   
   # Categorize
   categories = {
       'Cross-Region': 0,
       'Internet Egress': 0,
       'Cross-AZ': 0,
       'Other': 0
   }
   
   for usage_type, cost in transfer_costs.items():
       if 'Regional' in usage_type or 'Inter-Region' in usage_type:
           categories['Cross-Region'] += cost
       elif 'Out' in usage_type or 'Egress' in usage_type:
           categories['Internet Egress'] += cost
       elif 'AZ' in usage_type:
           categories['Cross-AZ'] += cost
       else:
           categories['Other'] += cost
   
   print("Data Transfer Costs by Category:")
   for category, cost in categories.items():
       print(f"{category}: ${cost:.2f}")
   ```

### 8.2 Implement VPC Endpoints

**Objective**: Reduce data transfer costs for AWS services.

**Actions**:

1. **Identify VPC Endpoint Opportunities**:
   ```python
   # identify_vpc_endpoint_opportunities.py
   import boto3
   
   ec2 = boto3.client('ec2')
   
   # Get VPC endpoints
   existing_endpoints = ec2.describe_vpc_endpoints()['VpcEndpoints']
   existing_services = {ep['ServiceName'] for ep in existing_endpoints}
   
   # Common services that benefit from VPC endpoints
   recommended_services = [
       'com.amazonaws.us-east-1.s3',  # S3
       'com.amazonaws.us-east-1.dynamodb',  # DynamoDB
       'com.amazonaws.us-east-1.ec2',  # EC2
       'com.amazonaws.us-east-1.ecr.api',  # ECR API
       'com.amazonaws.us-east-1.ecr.dkr',  # ECR Docker
       'com.amazonaws.us-east-1.logs',  # CloudWatch Logs
   ]
   
   missing_endpoints = [svc for svc in recommended_services if svc not in existing_services]
   
   if missing_endpoints:
       print("Recommended VPC Endpoints to create:")
       for service in missing_endpoints:
           print(f"  - {service}")
           print(f"    Estimated savings: $XXX/month")  # Calculate based on usage
   ```

2. **Create VPC Endpoint**:
   ```python
   # Create S3 VPC endpoint
   response = ec2.create_vpc_endpoint(
       VpcId='vpc-xxxxx',
       ServiceName='com.amazonaws.us-east-1.s3',
       RouteTableIds=['rtb-xxxxx', 'rtb-yyyyy'],
       VpcEndpointType='Gateway'
   )
   ```

### 8.3 Optimize CDN Usage

**Objective**: Reduce origin server load and data transfer costs.

**Actions**:

1. **CDN ROI Analysis**:
   ```markdown
   ## CloudFront CDN Analysis
   
   ### Current State (No CDN)
   - Origin bandwidth: 10 TB/month
   - Data transfer cost: $920/month ($0.09/GB)
   - Origin server load: High
   
   ### With CloudFront
   - CloudFront bandwidth: 10 TB/month
   - CloudFront cost: $850/month ($0.085/GB)
   - Origin bandwidth: 2 TB/month (80% cache hit rate)
   - Origin data transfer: $184/month
   - Total cost: $1,034/month
   
   ### Analysis
   - Direct cost: Slightly higher ($114/month)
   - Benefits:
     * Reduced origin server load (80% reduction)
     * Can downsize origin servers: Save $500/month
     * Improved user experience (lower latency)
   - Net savings: $386/month
   ```

**Deliverable**: Network cost analysis report, data transfer optimization recommendations, VPC architecture improvements, projected network cost savings.

---

## Step 9: Development and Non-Production Environment Optimization

### 9.1 Implement Automated Shutdown Schedules

**Objective**: Reduce costs by shutting down non-production environments when not in use.

**Actions**:

1. **Create Shutdown Lambda Function**:
   ```python
   # auto_shutdown.py
   import boto3
   import os
   from datetime import datetime
   
   ec2 = boto3.client('ec2')
   rds = boto3.client('rds')
   
   def lambda_handler(event, context):
       action = os.environ.get('ACTION', 'stop')  # 'stop' or 'start'
       
       # Get current time
       now = datetime.now()
       hour = now.hour
       day = now.weekday()  # 0 = Monday, 6 = Sunday
       
       # Define schedules
       schedules = {
           'development': {
               'weekday_start': 8,  # 8 AM
               'weekday_stop': 19,  # 7 PM
               'weekend': 'stopped'  # Stopped all weekend
           },
           'staging': {
               'weekday_start': 7,  # 7 AM
               'weekday_stop': 22,  # 10 PM
               'weekend': 'stopped'
           }
       }
       
       # Determine if resources should be running
       for env, schedule in schedules.items():
           should_run = False
           
           if day < 5:  # Weekday
               if schedule['weekday_start'] <= hour < schedule['weekday_stop']:
                   should_run = True
           else:  # Weekend
               should_run = (schedule['weekend'] == 'running')
           
           # Get instances for this environment
           instances = ec2.describe_instances(
               Filters=[
                   {'Name': 'tag:Environment', 'Values': [env]},
                   {'Name': 'tag:AutoShutdown', 'Values': ['true']}
               ]
           )
           
           instance_ids = []
           for reservation in instances['Reservations']:
               for instance in reservation['Instances']:
                   instance_ids.append(instance['InstanceId'])
           
           # Start or stop instances
           if should_run and instance_ids:
               ec2.start_instances(InstanceIds=instance_ids)
               print(f"Started {len(instance_ids)} {env} instances")
           elif not should_run and instance_ids:
               ec2.stop_instances(InstanceIds=instance_ids)
               print(f"Stopped {len(instance_ids)} {env} instances")
   ```

2. **Create EventBridge Schedule**:
   ```json
   {
     "ScheduleExpression": "cron(0 * * * ? *)",
     "State": "ENABLED",
     "Target": {
       "Arn": "arn:aws:lambda:us-east-1:123456789012:function:auto-shutdown",
       "RoleArn": "arn:aws:iam::123456789012:role/EventBridgeRole"
     }
   }
   ```

### 9.2 Right-Size Non-Production Environments

**Objective**: Use smaller instances for dev/test environments.

**Actions**:

1. **Non-Production Sizing Strategy**:
   ```markdown
   ## Non-Production Sizing Guidelines
   
   ### Development
   - Size: 25% of production capacity
   - Rationale: Individual developer testing, low concurrency
   - Example: Production uses m5.xlarge → Dev uses m5.large or t3.large
   
   ### Staging
   - Size: 50% of production capacity
   - Rationale: Pre-production testing, moderate load testing
   - Example: Production uses m5.xlarge → Staging uses m5.large
   
   ### Testing/QA
   - Size: 25-50% of production capacity
   - Rationale: Automated testing, performance testing
   - Example: Production uses m5.xlarge → Testing uses m5.large
   
   ### Estimated Savings
   - Development: 75% cost reduction
   - Staging: 50% cost reduction
   - Overall non-prod: 60% cost reduction
   ```

### 9.3 Implement Ephemeral Environments

**Objective**: Create temporary environments for feature development.

**Actions**:

1. **Ephemeral Environment Strategy**:
   ```yaml
   # GitHub Actions workflow for ephemeral environments
   name: Create Ephemeral Environment
   
   on:
     pull_request:
       types: [opened, synchronize]
   
   jobs:
     deploy-ephemeral:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v3
         
         - name: Create environment name
           id: env
           run: |
             ENV_NAME="pr-${{ github.event.pull_request.number }}"
             echo "name=$ENV_NAME" >> $GITHUB_OUTPUT
         
         - name: Deploy to ECS
           run: |
             # Deploy containerized app to ECS with unique task definition
             aws ecs register-task-definition \
               --family "myapp-${{ steps.env.outputs.name }}" \
               --container-definitions file://task-def.json
             
             aws ecs create-service \
               --cluster ephemeral-cluster \
               --service-name "${{ steps.env.outputs.name }}" \
               --task-definition "myapp-${{ steps.env.outputs.name }}" \
               --desired-count 1
         
         - name: Comment PR with URL
           uses: actions/github-script@v6
           with:
             script: |
               github.rest.issues.createComment({
                 issue_number: context.issue.number,
                 owner: context.repo.owner,
                 repo: context.repo.repo,
                 body: `Ephemeral environment deployed: https://${{ steps.env.outputs.name }}.example.com`
               })
   
   # Cleanup when PR is closed
   on:
     pull_request:
       types: [closed]
   
   jobs:
     cleanup-ephemeral:
       runs-on: ubuntu-latest
       steps:
         - name: Delete ECS service
           run: |
             ENV_NAME="pr-${{ github.event.pull_request.number }}"
             aws ecs delete-service \
               --cluster ephemeral-cluster \
               --service "$ENV_NAME" \
               --force
   ```

**Deliverable**: Non-production environment inventory, shutdown schedule recommendations, right-sizing recommendations, ephemeral environment strategy, projected savings.

---

## Step 10: Monitoring, Governance, and Continuous Optimization

### 10.1 Implement Cost Anomaly Detection

**Objective**: Automatically detect unusual cost spikes.

**Actions**:

1. **AWS Cost Anomaly Detection**:
   ```python
   # setup_anomaly_detection.py
   import boto3
   
   ce = boto3.client('ce')
   
   # Create anomaly monitor
   monitor_response = ce.create_anomaly_monitor(
       AnomalyMonitor={
           'MonitorName': 'TotalCostMonitor',
           'MonitorType': 'DIMENSIONAL',
           'MonitorDimension': 'SERVICE'
       }
   )
   
   monitor_arn = monitor_response['MonitorArn']
   
   # Create anomaly subscription
   subscription_response = ce.create_anomaly_subscription(
       AnomalySubscription={
           'SubscriptionName': 'CostAnomalyAlerts',
           'Threshold': 100.0,  # Alert if anomaly > $100
           'Frequency': 'DAILY',
           'MonitorArnList': [monitor_arn],
           'Subscribers': [
               {
                   'Type': 'EMAIL',
                   'Address': 'finops-team@company.com'
               },
               {
                   'Type': 'SNS',
                   'Address': 'arn:aws:sns:us-east-1:123456789012:cost-anomalies'
               }
           ]
       }
   )
   ```

### 10.2 Set Up Budget Alerts

**Objective**: Proactively monitor spending against budgets.

**Actions**:

1. **Create AWS Budgets**:
   ```python
   # create_budgets.py
   import boto3
   
   budgets = boto3.client('budgets')
   
   # Overall monthly budget
   budgets.create_budget(
       AccountId='123456789012',
       Budget={
           'BudgetName': 'MonthlyCloudBudget',
           'BudgetLimit': {
               'Amount': '50000',
               'Unit': 'USD'
           },
           'TimeUnit': 'MONTHLY',
           'BudgetType': 'COST'
       },
       NotificationsWithSubscribers=[
           {
               'Notification': {
                   'NotificationType': 'ACTUAL',
                   'ComparisonOperator': 'GREATER_THAN',
                   'Threshold': 80,
                   'ThresholdType': 'PERCENTAGE'
               },
               'Subscribers': [
                   {
                       'SubscriptionType': 'EMAIL',
                       'Address': 'finops-team@company.com'
                   }
               ]
           },
           {
               'Notification': {
                   'NotificationType': 'FORECASTED',
                   'ComparisonOperator': 'GREATER_THAN',
                   'Threshold': 100,
                   'ThresholdType': 'PERCENTAGE'
               },
               'Subscribers': [
                   {
                       'SubscriptionType': 'EMAIL',
                       'Address': 'cfo@company.com'
                   }
               ]
           }
       ]
   )
   
   # Team-specific budgets
   teams = ['engineering', 'data-science', 'product']
   team_budgets = {'engineering': 20000, 'data-science': 15000, 'product': 10000}
   
   for team in teams:
       budgets.create_budget(
           AccountId='123456789012',
           Budget={
               'BudgetName': f'{team}-monthly-budget',
               'BudgetLimit': {
                   'Amount': str(team_budgets[team]),
                   'Unit': 'USD'
               },
               'TimeUnit': 'MONTHLY',
               'BudgetType': 'COST',
               'CostFilters': {
                   'TagKeyValue': [f'user:Team${team}']
               }
           },
           NotificationsWithSubscribers=[
               {
                   'Notification': {
                       'NotificationType': 'ACTUAL',
                       'ComparisonOperator': 'GREATER_THAN',
                       'Threshold': 90,
                       'ThresholdType': 'PERCENTAGE'
                   },
                   'Subscribers': [
                       {
                           'SubscriptionType': 'EMAIL',
                           'Address': f'{team}-lead@company.com'
                       }
                   ]
               }
           ]
       )
   ```

### 10.3 Establish Tagging Policies

**Objective**: Ensure all resources are properly tagged for cost allocation.

**Actions**:

1. **Define Tagging Strategy**:
   ```markdown
   ## Required Tags
   
   All resources must have the following tags:
   
   - **Environment**: production | staging | development | testing
   - **Team**: Team name (e.g., engineering, data-science, product)
   - **Project**: Project name or code
   - **CostCenter**: Cost center for chargeback
   - **Owner**: Email of resource owner
   - **Application**: Application name
   
   Optional tags:
   - **Backup**: true | false (for backup automation)
   - **AutoShutdown**: true | false (for automated shutdown)
   - **Compliance**: HIPAA | PCI-DSS | SOC2 (if applicable)
   ```

2. **Enforce Tagging with Service Control Policies**:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "RequireTagsOnEC2",
         "Effect": "Deny",
         "Action": "ec2:RunInstances",
         "Resource": "arn:aws:ec2:*:*:instance/*",
         "Condition": {
           "StringNotLike": {
             "aws:RequestTag/Environment": ["production", "staging", "development", "testing"],
             "aws:RequestTag/Team": "*",
             "aws:RequestTag/Project": "*",
             "aws:RequestTag/CostCenter": "*",
             "aws:RequestTag/Owner": "*"
           }
         }
       }
     ]
   }
   ```

### 10.4 Create Cost Optimization Runbooks

**Objective**: Document ongoing optimization procedures.

**Actions**:

1. **Weekly Cost Review Runbook**:
   ```markdown
   # Weekly Cost Review Runbook
   
   ## Frequency
   Every Monday at 10 AM
   
   ## Participants
   - FinOps lead
   - Engineering manager
   - Team leads (as needed)
   
   ## Agenda
   
   ### 1. Review Last Week's Costs (15 min)
   - Compare to previous week
   - Identify any anomalies or spikes
   - Review budget vs actual
   
   ### 2. Review Cost Anomalies (10 min)
   - Check AWS Cost Anomaly Detection alerts
   - Investigate any flagged anomalies
   - Assign owners for investigation
   
   ### 3. Review Optimization Opportunities (15 min)
   - Check AWS Trusted Advisor recommendations
   - Review right-sizing recommendations
   - Check for idle resources
   
   ### 4. Track Optimization Initiatives (10 min)
   - Review status of ongoing optimizations
   - Update savings tracking
   - Identify blockers
   
   ### 5. Plan Next Week (10 min)
   - Prioritize new optimizations
   - Assign owners and deadlines
   - Schedule any needed deep dives
   
   ## Action Items Template
   - [ ] Action item description
   - Owner: Name
   - Due date: YYYY-MM-DD
   - Estimated savings: $X,XXX/month
   ```

**Deliverable**: Cost monitoring and alerting configuration, budget and alert definitions, cost allocation dashboards, tagging policy and enforcement, cost governance framework, runbooks.

---

## Conclusion

Following these detailed instructions will result in comprehensive cost optimization that:

- Reduces cloud spending by 20-50% on average
- Maintains or improves performance and reliability
- Establishes ongoing cost governance and monitoring
- Embeds cost awareness in team culture
- Provides clear ROI and business value

Remember that cost optimization is an ongoing practice, not a one-time project. Continuously monitor costs, implement new optimizations, and adapt to changing business needs to maintain cost efficiency over time.
