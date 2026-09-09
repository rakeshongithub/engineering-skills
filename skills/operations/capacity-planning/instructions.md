# Capacity Planning - Step-by-Step Instructions

## Overview

This document provides detailed, actionable instructions for estimating and planning infrastructure capacity to meet current and future demand. Follow these steps sequentially to create comprehensive capacity plans that balance performance, cost, and reliability.

## Prerequisites

Before starting, ensure you have:

- Access to infrastructure monitoring and metrics systems
- Historical performance and usage data (minimum 6 months)
- Understanding of application architecture and workload characteristics
- Business growth projections and requirements
- Budget and cost constraints
- Stakeholder access for requirements gathering

## Step 1: Data Collection and Current State Assessment

### 1.1 Infrastructure Inventory

**Objective**: Document all infrastructure components and their current configurations.

**Actions**:
1. Create infrastructure inventory spreadsheet:
   ```markdown
   | Component | Type | Specification | Quantity | Location | Cost/Month |
   |-----------|------|---------------|----------|----------|------------|
   | Web Server | EC2 | m5.xlarge | 20 | us-east-1 | $3,000 |
   | Database | RDS | db.r5.2xlarge | 2 | us-east-1 | $1,800 |
   | Cache | ElastiCache | r5.large | 3 | us-east-1 | $450 |
   ```

2. Document for each component:
   - Instance types and sizes
   - CPU, memory, storage specifications
   - Network bandwidth allocation
   - Current resource allocations
   - Geographic distribution

3. Calculate current costs:
   - Compute costs (instances, containers, serverless)
   - Storage costs (databases, object storage, block storage)
   - Network costs (data transfer, load balancers)
   - Licensing costs
   - Total monthly infrastructure cost

### 1.2 Collect Historical Metrics

**Objective**: Gather performance and utilization data over time.

**Actions**:
1. Export metrics from monitoring systems:
   ```bash
   # Example: Export CloudWatch metrics
   aws cloudwatch get-metric-statistics \
     --namespace AWS/EC2 \
     --metric-name CPUUtilization \
     --dimensions Name=InstanceId,Value=i-1234567890abcdef0 \
     --start-time 2025-03-09T00:00:00Z \
     --end-time 2025-09-09T00:00:00Z \
     --period 3600 \
     --statistics Average,Maximum \
     --output json > cpu_metrics.json
   ```

2. Collect key metrics:
   - **Compute**: CPU utilization, memory usage, instance count
   - **Storage**: Disk usage, IOPS, throughput
   - **Network**: Bandwidth, connections, requests per second
   - **Database**: Query rate, connection count, replication lag
   - **Application**: Request rate, response time, error rate

3. Analyze time periods:
   - Hourly patterns (identify peak hours)
   - Daily patterns (weekday vs. weekend)
   - Weekly patterns (identify weekly cycles)
   - Monthly patterns (identify month-end spikes)
   - Yearly patterns (identify seasonal trends)

4. Create visualization dashboards:
   ```python
   import pandas as pd
   import matplotlib.pyplot as plt
   
   # Load metrics data
   df = pd.read_csv('cpu_metrics.csv')
   df['timestamp'] = pd.to_datetime(df['timestamp'])
   
   # Plot CPU utilization over time
   plt.figure(figsize=(12, 6))
   plt.plot(df['timestamp'], df['cpu_utilization'])
   plt.axhline(y=70, color='r', linestyle='--', label='Target (70%)')
   plt.xlabel('Date')
   plt.ylabel('CPU Utilization (%)')
   plt.title('CPU Utilization Trend')
   plt.legend()
   plt.savefig('cpu_trend.png')
   ```

### 1.3 Identify Current Bottlenecks

**Objective**: Understand current performance constraints.

**Actions**:
1. Analyze resource saturation:
   - Identify resources consistently above 70% utilization
   - Find resources hitting limits (connections, IOPS, bandwidth)
   - Document performance degradation patterns

2. Review incident history:
   - Capacity-related incidents in past 6 months
   - Performance degradation events
   - Outages due to resource exhaustion

3. Interview stakeholders:
   - Development teams: Application performance issues
   - Operations teams: Infrastructure constraints
   - Business teams: Customer complaints related to performance

**Deliverable**: Current state assessment document with infrastructure inventory, metrics analysis, and bottleneck identification.

---

## Step 2: Business Requirements Analysis

### 2.1 Gather Growth Projections

**Objective**: Understand business growth expectations.

**Actions**:
1. Interview business stakeholders:
   ```markdown
   ## Growth Interview Questions
   
   1. What is the expected user growth over the next 6 months? 1 year? 3 years?
   2. Are there any planned product launches or major features?
   3. What marketing campaigns are scheduled?
   4. Are there seasonal patterns in your business?
   5. What are the revenue growth targets?
   6. Are there any geographic expansion plans?
   7. What are the key business drivers of infrastructure demand?
   ```

2. Document growth projections:
   ```markdown
   ## Business Growth Projections
   
   **User Growth**:
   - Current: 100,000 monthly active users
   - 6 months: 150,000 MAU (50% growth)
   - 1 year: 250,000 MAU (150% growth)
   - 3 years: 1,000,000 MAU (10x growth)
   
   **Transaction Volume**:
   - Current: 1 million transactions/month
   - 6 months: 1.8 million/month (80% growth)
   - 1 year: 3.5 million/month (250% growth)
   
   **Geographic Expansion**:
   - Q3 2026: Launch in Europe (expect 30% of US traffic)
   - Q1 2027: Launch in Asia (expect 50% of US traffic)
   ```

### 2.2 Define SLA Requirements

**Objective**: Establish performance and availability targets.

**Actions**:
1. Document SLA requirements:
   ```markdown
   ## SLA Requirements
   
   **Availability**:
   - Target: 99.9% uptime (43.2 minutes downtime/month)
   - Measurement: Monthly uptime percentage
   - Penalty: 10% service credit for each 0.1% below target
   
   **Performance**:
   - Response time p95: < 500ms
   - Response time p99: < 1,000ms
   - Error rate: < 0.1%
   - Throughput: Support 10,000 requests/second
   
   **Data Durability**:
   - RPO (Recovery Point Objective): < 1 hour
   - RTO (Recovery Time Objective): < 4 hours
   ```

2. Identify compliance requirements:
   - Data residency requirements
   - Regulatory compliance (GDPR, HIPAA, PCI-DSS)
   - Audit and logging requirements
   - Data retention policies

### 2.3 Understand Budget Constraints

**Objective**: Define cost boundaries and priorities.

**Actions**:
1. Document budget information:
   ```markdown
   ## Budget Constraints
   
   **Current Infrastructure Cost**: $50,000/month
   
   **Budget Allocation**:
   - Year 1: $75,000/month (50% increase allowed)
   - Year 2: $100,000/month (100% increase allowed)
   - Year 3: $150,000/month (200% increase allowed)
   
   **Cost Priorities**:
   1. Meet SLA requirements (non-negotiable)
   2. Support business growth (high priority)
   3. Optimize costs (medium priority)
   4. Enable new features (low priority)
   ```

**Deliverable**: Business requirements document with growth projections, SLA requirements, and budget constraints.

---

## Step 3: Workload Characterization

### 3.1 Analyze Resource Consumption Patterns

**Objective**: Understand how application consumes resources.

**Actions**:
1. Measure resource consumption per unit:
   ```python
   # Calculate resource consumption per user
   total_cpu_hours = 1000  # Total CPU hours in a month
   total_users = 100000    # Monthly active users
   
   cpu_per_user = total_cpu_hours / total_users
   print(f"CPU hours per user: {cpu_per_user}")
   
   # Calculate resource consumption per transaction
   total_requests = 1000000  # Total requests in a month
   total_memory_gb = 5000    # Total memory GB-hours
   
   memory_per_request = total_memory_gb / total_requests
   print(f"Memory GB per 1000 requests: {memory_per_request * 1000}")
   ```

2. Profile different workload types:
   ```markdown
   ## Workload Profiles
   
   **Web Requests**:
   - CPU: 10ms per request
   - Memory: 50 MB per concurrent request
   - Network: 100 KB per request
   - Database queries: 2-5 per request
   
   **Batch Jobs**:
   - CPU: 2 hours per job
   - Memory: 8 GB per job
   - Storage: 100 GB temporary
   - Frequency: 10 jobs/day
   
   **Background Tasks**:
   - CPU: Constant 20% of 1 core
   - Memory: 2 GB
   - Network: 1 Mbps
   ```

### 3.2 Identify Scaling Characteristics

**Objective**: Understand how application scales.

**Actions**:
1. Test horizontal scaling:
   - Add instances and measure throughput increase
   - Identify linear vs. sublinear scaling
   - Find scaling bottlenecks (database, shared resources)

2. Test vertical scaling:
   - Increase instance size and measure performance
   - Identify diminishing returns
   - Find optimal instance sizes

3. Document scaling limits:
   ```markdown
   ## Scaling Characteristics
   
   **Horizontal Scaling**:
   - Scales linearly up to 50 instances
   - Database becomes bottleneck beyond 50 instances
   - Requires read replicas for further scaling
   
   **Vertical Scaling**:
   - Optimal instance size: m5.2xlarge (8 vCPU, 32 GB RAM)
   - Larger instances show diminishing returns
   - Cost-effective sweet spot: m5.xlarge to m5.2xlarge
   
   **Limitations**:
   - Database connection limit: 1,000 connections
   - Single database write capacity: 5,000 writes/second
   - Network bandwidth per instance: 10 Gbps
   ```

**Deliverable**: Workload characterization report with resource consumption models and scaling characteristics.

---

## Step 4: Demand Forecasting

### 4.1 Select Forecasting Method

**Objective**: Choose appropriate forecasting approach.

**Actions**:
1. Evaluate data characteristics:
   - **Trend**: Is usage growing, declining, or stable?
   - **Seasonality**: Are there regular patterns (daily, weekly, yearly)?
   - **Volatility**: How much does usage vary?
   - **Data volume**: How much historical data is available?

2. Choose forecasting method:
   ```markdown
   ## Forecasting Method Selection
   
   **Use Time Series Analysis if**:
   - Have 12+ months of historical data
   - Clear trends and seasonal patterns
   - Relatively stable business
   
   **Use Regression Analysis if**:
   - Can identify clear drivers (users, transactions)
   - Have data on both usage and drivers
   - Want to model specific scenarios
   
   **Use Scenario Planning if**:
   - High uncertainty about future
   - Multiple possible business trajectories
   - Planning for major changes
   ```

### 4.2 Build Forecast Models

**Objective**: Create demand projections.

**Actions**:
1. Time series forecasting example:
   ```python
   import pandas as pd
   from statsmodels.tsa.holtwinters import ExponentialSmoothing
   
   # Load historical data
   df = pd.read_csv('monthly_users.csv', parse_dates=['month'])
   df.set_index('month', inplace=True)
   
   # Fit Holt-Winters model (trend + seasonality)
   model = ExponentialSmoothing(
       df['users'],
       trend='add',
       seasonal='add',
       seasonal_periods=12
   )
   fit = model.fit()
   
   # Forecast next 24 months
   forecast = fit.forecast(steps=24)
   
   # Plot results
   import matplotlib.pyplot as plt
   plt.figure(figsize=(12, 6))
   plt.plot(df.index, df['users'], label='Historical')
   plt.plot(forecast.index, forecast, label='Forecast', linestyle='--')
   plt.xlabel('Month')
   plt.ylabel('Users')
   plt.title('User Growth Forecast')
   plt.legend()
   plt.savefig('user_forecast.png')
   ```

2. Regression forecasting example:
   ```python
   from sklearn.linear_model import LinearRegression
   import numpy as np
   
   # Prepare data
   X = df[['marketing_spend', 'new_features']].values
   y = df['users'].values
   
   # Fit linear regression
   model = LinearRegression()
   model.fit(X, y)
   
   # Forecast based on planned marketing and features
   future_marketing = np.array([[100000, 5], [150000, 8], [200000, 10]])
   forecast = model.predict(future_marketing)
   
   print(f"Forecasted users: {forecast}")
   print(f"Coefficient for marketing: {model.coef_[0]}")
   print(f"Coefficient for features: {model.coef_[1]}")
   ```

### 4.3 Create Multiple Scenarios

**Objective**: Account for uncertainty in forecasts.

**Actions**:
1. Define scenarios:
   ```markdown
   ## Forecast Scenarios
   
   **Conservative (P10)**:
   - Assumption: Slower than expected growth
   - User growth: 30% year-over-year
   - Traffic growth: 40% year-over-year
   - Probability: 10%
   
   **Expected (P50)**:
   - Assumption: Growth as planned
   - User growth: 100% year-over-year
   - Traffic growth: 150% year-over-year
   - Probability: 50%
   
   **Aggressive (P90)**:
   - Assumption: Faster than expected growth
   - User growth: 200% year-over-year
   - Traffic growth: 300% year-over-year
   - Probability: 10%
   ```

2. Calculate confidence intervals:
   ```python
   # Calculate prediction intervals
   from scipy import stats
   
   # Forecast with confidence intervals
   forecast_result = fit.forecast(steps=24, return_conf_int=True)
   forecast_mean = forecast_result[0]
   forecast_ci = forecast_result[1]
   
   # Plot with confidence intervals
   plt.figure(figsize=(12, 6))
   plt.plot(df.index, df['users'], label='Historical')
   plt.plot(forecast_mean.index, forecast_mean, label='Forecast')
   plt.fill_between(
       forecast_ci.index,
       forecast_ci.iloc[:, 0],
       forecast_ci.iloc[:, 1],
       alpha=0.3,
       label='95% Confidence Interval'
   )
   plt.legend()
   plt.savefig('forecast_with_ci.png')
   ```

**Deliverable**: Demand forecast models with multiple scenarios and confidence intervals.

---

## Step 5: Capacity Modeling

### 5.1 Calculate Required Capacity

**Objective**: Translate demand forecasts into infrastructure requirements.

**Actions**:
1. Calculate compute capacity:
   ```python
   # Example capacity calculation
   
   # Forecasted demand
   forecasted_users = 250000  # MAU
   forecasted_requests_per_second = 10000  # Peak RPS
   
   # Resource consumption per unit (from workload characterization)
   cpu_ms_per_request = 10  # CPU milliseconds per request
   memory_mb_per_concurrent_request = 50  # Memory MB
   
   # Calculate required CPU
   cpu_cores_needed = (forecasted_requests_per_second * cpu_ms_per_request / 1000)
   print(f"CPU cores needed: {cpu_cores_needed}")
   
   # Calculate required memory
   concurrent_requests = forecasted_requests_per_second * 0.5  # Average request duration
   memory_gb_needed = (concurrent_requests * memory_mb_per_concurrent_request / 1024)
   print(f"Memory GB needed: {memory_gb_needed}")
   
   # Calculate instance count
   instance_cpu = 8  # vCPUs per instance
   instance_memory = 32  # GB per instance
   
   instances_for_cpu = cpu_cores_needed / instance_cpu
   instances_for_memory = memory_gb_needed / instance_memory
   instances_needed = max(instances_for_cpu, instances_for_memory)
   
   print(f"Instances needed: {int(instances_needed) + 1}")
   ```

2. Calculate storage capacity:
   ```python
   # Storage capacity calculation
   
   # Current state
   current_users = 100000
   current_storage_tb = 5
   
   # Storage per user
   storage_mb_per_user = (current_storage_tb * 1024 * 1024) / current_users
   
   # Forecasted storage
   forecasted_storage_tb = (forecasted_users * storage_mb_per_user) / (1024 * 1024)
   print(f"Forecasted storage: {forecasted_storage_tb} TB")
   
   # Add growth buffer
   storage_with_buffer = forecasted_storage_tb * 1.3  # 30% buffer
   print(f"Storage with buffer: {storage_with_buffer} TB")
   ```

3. Calculate network capacity:
   ```python
   # Network capacity calculation
   
   # Average request/response size
   avg_request_kb = 10
   avg_response_kb = 100
   
   # Calculate bandwidth
   bandwidth_mbps = forecasted_requests_per_second * (avg_request_kb + avg_response_kb) * 8 / 1024
   print(f"Required bandwidth: {bandwidth_mbps} Mbps")
   
   # Add headroom
   bandwidth_with_headroom = bandwidth_mbps * 1.5  # 50% headroom
   print(f"Bandwidth with headroom: {bandwidth_with_headroom} Mbps")
   ```

### 5.2 Add Capacity Headroom

**Objective**: Include buffer for uncertainty and growth.

**Actions**:
1. Determine appropriate headroom:
   ```markdown
   ## Headroom Strategy
   
   **Conservative (50-100% headroom)**:
   - Use when: High growth uncertainty, slow scaling
   - Example: 100 instances needed → provision 150-200
   
   **Moderate (20-50% headroom)**:
   - Use when: Moderate uncertainty, can scale in hours
   - Example: 100 instances needed → provision 120-150
   
   **Minimal (10-20% headroom)**:
   - Use when: Low uncertainty, fast auto-scaling
   - Example: 100 instances needed → provision 110-120
   ```

2. Calculate headroom:
   ```python
   # Apply headroom to capacity calculations
   headroom_percentage = 0.30  # 30% headroom
   
   instances_with_headroom = instances_needed * (1 + headroom_percentage)
   storage_with_headroom = forecasted_storage_tb * (1 + headroom_percentage)
   bandwidth_with_headroom = bandwidth_mbps * (1 + headroom_percentage)
   
   print(f"Instances with headroom: {int(instances_with_headroom) + 1}")
   print(f"Storage with headroom: {storage_with_headroom} TB")
   print(f"Bandwidth with headroom: {bandwidth_with_headroom} Mbps")
   ```

### 5.3 Account for Redundancy

**Objective**: Include capacity for high availability.

**Actions**:
1. Calculate redundancy requirements:
   ```markdown
   ## Redundancy Requirements
   
   **N+1 Redundancy**:
   - Minimum: Can lose 1 component without impact
   - Example: 10 instances → provision 11
   
   **N+2 Redundancy**:
   - Standard: Can lose 2 components without impact
   - Example: 10 instances → provision 12
   
   **Active-Active Multi-Region**:
   - Full capacity in 2+ regions
   - Example: 100 instances → 100 in each region
   ```

2. Apply redundancy:
   ```python
   # N+2 redundancy for instances
   instances_with_redundancy = instances_with_headroom + 2
   
   # Multi-region redundancy
   regions = 2
   total_instances = instances_with_redundancy * regions
   
   print(f"Instances with N+2 redundancy: {int(instances_with_redundancy)}")
   print(f"Total instances (multi-region): {int(total_instances)}")
   ```

**Deliverable**: Capacity requirement calculations with headroom and redundancy.

---

## Step 6: Cost Analysis and Optimization

### 6.1 Calculate Infrastructure Costs

**Objective**: Estimate costs for recommended capacity.

**Actions**:
1. Calculate compute costs:
   ```python
   # Compute cost calculation
   
   # Instance pricing (example: m5.2xlarge)
   on_demand_price_per_hour = 0.384
   reserved_price_per_hour = 0.230  # 1-year reserved
   hours_per_month = 730
   
   # Calculate costs
   instances = 50
   
   on_demand_cost = instances * on_demand_price_per_hour * hours_per_month
   reserved_cost = instances * reserved_price_per_hour * hours_per_month
   
   print(f"On-demand cost: ${on_demand_cost:,.2f}/month")
   print(f"Reserved cost: ${reserved_cost:,.2f}/month")
   print(f"Savings with reserved: ${on_demand_cost - reserved_cost:,.2f}/month")
   ```

2. Calculate storage costs:
   ```python
   # Storage cost calculation
   
   storage_tb = 20
   
   # S3 pricing (example)
   s3_standard_per_gb = 0.023
   s3_ia_per_gb = 0.0125
   
   # Assume 70% standard, 30% infrequent access
   standard_gb = storage_tb * 1024 * 0.7
   ia_gb = storage_tb * 1024 * 0.3
   
   storage_cost = (standard_gb * s3_standard_per_gb) + (ia_gb * s3_ia_per_gb)
   print(f"Storage cost: ${storage_cost:,.2f}/month")
   ```

3. Calculate total cost:
   ```python
   # Total cost calculation
   
   compute_cost = reserved_cost
   storage_cost = 500  # From above
   network_cost = 2000  # Data transfer
   database_cost = 5000  # RDS instances
   other_cost = 1000  # Load balancers, etc.
   
   total_cost = compute_cost + storage_cost + network_cost + database_cost + other_cost
   
   print(f"\nTotal Infrastructure Cost: ${total_cost:,.2f}/month")
   print(f"Annual cost: ${total_cost * 12:,.2f}")
   ```

### 6.2 Identify Cost Optimization Opportunities

**Objective**: Find ways to reduce costs while meeting requirements.

**Actions**:
1. Evaluate pricing options:
   ```markdown
   ## Cost Optimization Strategies
   
   **Reserved Instances**:
   - Savings: 30-40% vs. on-demand
   - Use for: Baseline capacity
   - Commitment: 1 or 3 years
   
   **Spot Instances**:
   - Savings: 70-90% vs. on-demand
   - Use for: Fault-tolerant workloads
   - Risk: Can be interrupted
   
   **Savings Plans**:
   - Savings: 20-30% vs. on-demand
   - Flexibility: Can change instance types
   - Commitment: 1 or 3 years
   
   **Right-Sizing**:
   - Savings: 10-30%
   - Action: Match instance size to actual usage
   - Tool: AWS Compute Optimizer, etc.
   ```

2. Calculate optimization savings:
   ```python
   # Cost optimization calculation
   
   # Current cost (all on-demand)
   current_cost = 100000  # $100k/month
   
   # Optimizations
   reserved_instances_savings = current_cost * 0.30  # 30% of compute
   spot_instances_savings = current_cost * 0.10  # 10% of compute
   right_sizing_savings = current_cost * 0.15  # 15% overall
   storage_tiering_savings = current_cost * 0.05  # 5% of storage
   
   total_savings = (
       reserved_instances_savings +
       spot_instances_savings +
       right_sizing_savings +
       storage_tiering_savings
   )
   
   optimized_cost = current_cost - total_savings
   
   print(f"Current cost: ${current_cost:,.2f}/month")
   print(f"Total savings: ${total_savings:,.2f}/month ({total_savings/current_cost*100:.1f}%)")
   print(f"Optimized cost: ${optimized_cost:,.2f}/month")
   ```

**Deliverable**: Cost analysis with optimization recommendations and projected savings.

---

## Step 7: Risk Assessment and Mitigation

### 7.1 Identify Capacity Risks

**Objective**: Understand potential capacity-related risks.

**Actions**:
1. Create risk matrix:
   ```markdown
   | Risk | Likelihood | Impact | Severity | Mitigation |
   |------|-----------|--------|----------|------------|
   | Faster than expected growth | Medium | High | High | Maintain 30% headroom, monitor weekly |
   | Database bottleneck | High | High | Critical | Add read replicas, plan sharding |
   | Regional outage | Low | High | Medium | Multi-region deployment |
   | Cost overrun | Medium | Medium | Medium | Monthly cost reviews, alerts |
   | Slow scaling | Low | Medium | Low | Pre-provision capacity, auto-scaling |
   ```

2. Assess each risk:
   - **Likelihood**: Low, Medium, High
   - **Impact**: Low, Medium, High
   - **Severity**: Likelihood × Impact
   - **Mitigation**: Specific actions to reduce risk

### 7.2 Develop Mitigation Strategies

**Objective**: Create plans to address identified risks.

**Actions**:
1. For each high-severity risk, define:
   ```markdown
   ## Risk Mitigation Plan: Database Bottleneck
   
   **Risk Description**: Database becomes bottleneck as traffic grows
   
   **Current State**: Single master database, 2 read replicas
   
   **Trigger Points**:
   - CPU utilization > 70% for 1 hour
   - Connection count > 800 (80% of limit)
   - Replication lag > 5 seconds
   
   **Mitigation Actions**:
   1. Short-term: Add 3 more read replicas
   2. Medium-term: Upgrade master instance size
   3. Long-term: Implement sharding architecture
   
   **Contingency Plan**:
   - Emergency read replica provisioning (< 1 hour)
   - Database connection pooling implementation
   - Query optimization sprint
   
   **Owner**: Database team lead
   **Review**: Monthly
   ```

**Deliverable**: Risk assessment matrix with mitigation strategies for each risk.

---

## Step 8: Implementation Planning

### 8.1 Create Phased Roadmap

**Objective**: Plan incremental capacity additions.

**Actions**:
1. Define implementation phases:
   ```markdown
   ## Implementation Roadmap
   
   **Phase 1: Immediate (Month 1-2)**
   - Objective: Address current bottlenecks
   - Actions:
     - Add 20 application instances
     - Add 2 database read replicas
     - Increase storage from 5 TB to 10 TB
   - Cost: $15,000/month increase
   - Success criteria: Queue time < 1 second, CPU < 70%
   
   **Phase 2: Short-term (Month 3-6)**
   - Objective: Support 50% user growth
   - Actions:
     - Implement auto-scaling (10-50 instances)
     - Add caching layer (Redis)
     - Optimize database queries
   - Cost: $10,000/month increase
   - Success criteria: Support 150,000 MAU, p95 latency < 500ms
   
   **Phase 3: Medium-term (Month 7-12)**
   - Objective: Support 100% user growth
   - Actions:
     - Add multi-region deployment
     - Implement CDN
     - Database sharding preparation
   - Cost: $25,000/month increase
   - Success criteria: Support 250,000 MAU, 99.9% uptime
   ```

2. Create Gantt chart:
   ```python
   import matplotlib.pyplot as plt
   import matplotlib.dates as mdates
   from datetime import datetime, timedelta
   
   # Define tasks
   tasks = [
       ('Add instances', datetime(2026, 9, 1), datetime(2026, 9, 15)),
       ('Add read replicas', datetime(2026, 9, 10), datetime(2026, 9, 25)),
       ('Increase storage', datetime(2026, 9, 1), datetime(2026, 9, 10)),
       ('Implement auto-scaling', datetime(2026, 10, 1), datetime(2026, 11, 1)),
       ('Add caching', datetime(2026, 10, 15), datetime(2026, 11, 15)),
       ('Multi-region deployment', datetime(2026, 12, 1), datetime(2027, 2, 1)),
   ]
   
   # Create Gantt chart
   fig, ax = plt.subplots(figsize=(12, 6))
   
   for i, (task, start, end) in enumerate(tasks):
       ax.barh(i, (end - start).days, left=start, height=0.5)
       ax.text(start, i, f' {task}', va='center')
   
   ax.set_yticks(range(len(tasks)))
   ax.set_yticklabels([task[0] for task in tasks])
   ax.xaxis.set_major_formatter(mdates.DateFormatter('%Y-%m'))
   plt.xticks(rotation=45)
   plt.xlabel('Timeline')
   plt.title('Capacity Implementation Roadmap')
   plt.tight_layout()
   plt.savefig('implementation_roadmap.png')
   ```

### 8.2 Define Success Criteria

**Objective**: Establish measurable goals for each phase.

**Actions**:
1. Define metrics for success:
   ```markdown
   ## Success Criteria
   
   **Phase 1 Success Criteria**:
   - [ ] CPU utilization < 70% during peak hours
   - [ ] Memory utilization < 80%
   - [ ] Database connection count < 80% of limit
   - [ ] p95 response time < 500ms
   - [ ] Error rate < 0.1%
   - [ ] Zero capacity-related incidents
   - [ ] Cost within 5% of budget
   
   **Validation Method**:
   - Monitor metrics for 2 weeks post-implementation
   - Conduct load testing at 120% of expected peak
   - Review incident reports
   - Cost analysis report
   ```

**Deliverable**: Implementation roadmap with phases, timelines, and success criteria.

---

## Step 9: Monitoring and Alerting Design

### 9.1 Define Capacity Metrics

**Objective**: Establish metrics to track capacity utilization.

**Actions**:
1. Identify key metrics:
   ```markdown
   ## Capacity Metrics
   
   **Compute Metrics**:
   - CPU utilization (average, p95, p99)
   - Memory utilization
   - Instance count
   - Auto-scaling events
   
   **Storage Metrics**:
   - Disk usage (GB, percentage)
   - IOPS utilization
   - Throughput (MB/s)
   - Growth rate (GB/day)
   
   **Network Metrics**:
   - Bandwidth utilization
   - Connection count
   - Packet loss rate
   - Latency
   
   **Database Metrics**:
   - Connection count
   - Query rate (queries/second)
   - Replication lag
   - IOPS utilization
   
   **Application Metrics**:
   - Request rate (requests/second)
   - Response time (p50, p95, p99)
   - Error rate
   - Concurrent users
   ```

### 9.2 Set Alert Thresholds

**Objective**: Define when to alert on capacity issues.

**Actions**:
1. Define alert levels:
   ```markdown
   ## Alert Thresholds
   
   **Warning Alerts (70-80% utilization)**:
   - Purpose: Early warning, plan capacity addition
   - Action: Review capacity plan, prepare to scale
   - Escalation: Email to ops team
   - Example: CPU utilization > 70% for 1 hour
   
   **Critical Alerts (80-90% utilization)**:
   - Purpose: Immediate attention needed
   - Action: Scale capacity within 24 hours
   - Escalation: Page on-call engineer
   - Example: CPU utilization > 85% for 30 minutes
   
   **Emergency Alerts (>90% utilization)**:
   - Purpose: Capacity exhaustion imminent
   - Action: Emergency scaling, incident response
   - Escalation: Page on-call + manager
   - Example: CPU utilization > 90% for 15 minutes
   ```

2. Configure alerts:
   ```yaml
   # Example: CloudWatch alarm configuration
   CPUUtilizationAlarm:
     Type: AWS::CloudWatch::Alarm
     Properties:
       AlarmName: HighCPUUtilization
       AlarmDescription: CPU utilization is too high
       MetricName: CPUUtilization
       Namespace: AWS/EC2
       Statistic: Average
       Period: 300
       EvaluationPeriods: 2
       Threshold: 70
       ComparisonOperator: GreaterThanThreshold
       AlarmActions:
         - !Ref SNSTopicArn
   ```

### 9.3 Create Capacity Dashboards

**Objective**: Visualize capacity utilization and trends.

**Actions**:
1. Design dashboard layout:
   ```markdown
   ## Capacity Dashboard
   
   **Section 1: Executive Summary**
   - Current utilization vs. capacity
   - Headroom remaining (percentage)
   - Projected time to capacity exhaustion
   - Cost vs. budget
   
   **Section 2: Compute Capacity**
   - CPU utilization trend (7 days)
   - Memory utilization trend
   - Instance count over time
   - Auto-scaling events
   
   **Section 3: Storage Capacity**
   - Disk usage trend
   - IOPS utilization
   - Growth rate
   - Projected time to full
   
   **Section 4: Database Capacity**
   - Connection count
   - Query rate
   - Replication lag
   - IOPS utilization
   
   **Section 5: Alerts and Incidents**
   - Active capacity alerts
   - Recent capacity incidents
   - Alert history
   ```

**Deliverable**: Monitoring strategy with metrics, alerts, and dashboard designs.

---

## Step 10: Documentation and Knowledge Transfer

### 10.1 Create Capacity Planning Document

**Objective**: Document complete capacity plan.

**Actions**:
1. Write comprehensive document:
   ```markdown
   # Capacity Planning Document
   
   ## Executive Summary
   - Current state and challenges
   - Recommended capacity plan
   - Cost implications
   - Implementation timeline
   - Key risks and mitigations
   
   ## Current State Assessment
   - Infrastructure inventory
   - Historical metrics analysis
   - Current bottlenecks
   - Utilization baselines
   
   ## Business Requirements
   - Growth projections
   - SLA requirements
   - Budget constraints
   - Compliance requirements
   
   ## Workload Characterization
   - Resource consumption models
   - Scaling characteristics
   - Performance profiles
   
   ## Demand Forecasting
   - Forecasting methodology
   - Demand projections (6mo, 1yr, 3yr)
   - Scenarios and confidence intervals
   - Assumptions and constraints
   
   ## Capacity Requirements
   - Compute capacity sizing
   - Storage capacity sizing
   - Network capacity sizing
   - Database capacity sizing
   - Headroom and redundancy
   
   ## Cost Analysis
   - Current costs
   - Projected costs
   - Cost optimization opportunities
   - ROI analysis
   
   ## Risk Assessment
   - Risk matrix
   - Mitigation strategies
   - Contingency plans
   
   ## Implementation Plan
   - Phased roadmap
   - Timeline and milestones
   - Success criteria
   - Rollback procedures
   
   ## Monitoring and Alerting
   - Capacity metrics
   - Alert thresholds
   - Dashboard designs
   - Review cadence
   
   ## Appendices
   - Detailed calculations
   - Data sources
   - Stakeholder interviews
   - References
   ```

### 10.2 Create Operational Runbooks

**Objective**: Enable operations team to manage capacity.

**Actions**:
1. Write runbooks for common scenarios:
   ```markdown
   # Runbook: Emergency Capacity Scaling
   
   ## When to Use
   - CPU utilization > 90% for 15 minutes
   - Memory utilization > 95%
   - Request queue depth > 1000
   - Error rate > 1%
   
   ## Prerequisites
   - Access to AWS console or CLI
   - On-call engineer paged
   - Incident created in PagerDuty
   
   ## Procedure
   
   ### Step 1: Assess Situation
   1. Check capacity dashboard
   2. Identify bottleneck (CPU, memory, database, etc.)
   3. Verify this is capacity issue, not application bug
   
   ### Step 2: Immediate Mitigation
   1. Increase auto-scaling max instance count:
      ```bash
      aws autoscaling update-auto-scaling-group \
        --auto-scaling-group-name web-asg \
        --max-size 100
      ```
   2. Manually trigger scale-out if needed:
      ```bash
      aws autoscaling set-desired-capacity \
        --auto-scaling-group-name web-asg \
        --desired-capacity 80
      ```
   
   ### Step 3: Monitor
   1. Watch instance launch progress
   2. Monitor CPU and memory utilization
   3. Verify error rate decreasing
   4. Check application logs for issues
   
   ### Step 4: Communicate
   1. Update incident with actions taken
   2. Notify stakeholders of situation
   3. Provide ETA for resolution
   
   ### Step 5: Post-Incident
   1. Schedule capacity planning review
   2. Update capacity plan if needed
   3. Document lessons learned
   ```

### 10.3 Conduct Training

**Objective**: Ensure team understands capacity plan.

**Actions**:
1. Prepare training materials:
   - Presentation slides
   - Hands-on exercises
   - Quiz/assessment
   - Reference guides

2. Conduct training sessions:
   - Overview of capacity plan
   - How to read capacity dashboards
   - When and how to scale
   - Emergency procedures
   - Q&A session

3. Establish support:
   - Office hours for questions
   - Slack channel for capacity discussions
   - Wiki with FAQs
   - Regular capacity reviews

**Deliverable**: Complete documentation, operational runbooks, and training materials.

---

## Conclusion

Following these detailed instructions will result in a comprehensive capacity plan that:

- Accurately forecasts future capacity needs based on business growth
- Sizes infrastructure appropriately for performance and cost
- Identifies and mitigates capacity-related risks
- Provides clear implementation roadmap
- Enables proactive capacity management through monitoring
- Equips team with knowledge and tools to execute plan

Remember that capacity planning is an ongoing process. Regularly review actual usage against forecasts, update projections based on business changes, and continuously optimize for cost and performance.
