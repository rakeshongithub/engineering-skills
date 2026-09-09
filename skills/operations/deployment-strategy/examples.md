# Deployment Strategy Design - Comprehensive Examples

This document provides four detailed, real-world examples of deployment strategy design covering diverse scenarios, industries, and approaches.

## Example 1: E-Commerce Platform - Canary Deployment with Istio

[See SKILL.md Example 1 for complete details]

**Summary**: High-traffic e-commerce platform with microservices on Kubernetes, using canary deployment with Istio service mesh for sophisticated traffic management and automated rollback based on metrics.

**Key Highlights**:
- Deployment frequency increased from weekly to 3-5 times per week
- Zero revenue-impacting outages since implementation
- Issues detected and rolled back automatically before affecting >10% of users
- Average rollback time: 2 minutes
- Deployment confidence increased significantly
- A/B testing enabled for new features

**Strategy**: Canary Deployment with Istio + Feature Flags

**Infrastructure**: Kubernetes with Istio service mesh, Prometheus monitoring, automated canary progression

---

## Example 2: Banking Application - Blue/Green Deployment

[See SKILL.md Example 2 for complete details]

**Summary**: Core banking application with strict regulatory compliance requirements, using blue/green deployment to achieve near-zero downtime and instant rollback capability while maintaining complete audit trail.

**Key Highlights**:
- Deployment downtime reduced from 4 hours to 30 seconds
- Rollback time: < 30 seconds (load balancer switch)
- Zero failed deployments since implementation
- Complete audit trail maintained for compliance
- Deployment confidence increased significantly
- Ability to validate before user impact

**Strategy**: Blue/Green Deployment with Database Versioning

**Infrastructure**: AWS EC2 instances, Application Load Balancer, backward-compatible database migrations

---

## Example 3: Mobile API - Rolling Deployment with Health Checks

[See SKILL.md Example 3 for complete details]

**Summary**: Mobile application backend API with budget constraints, using rolling deployment with sophisticated health checks to achieve daily deployments without user-facing errors while minimizing infrastructure costs.

**Key Highlights**:
- Daily deployments with zero user-facing errors
- Infrastructure costs minimized (no duplicate environments)
- 99.95% availability maintained (exceeding 99.9% SLA)
- Zero manual rollbacks needed
- Automatic rollback on health check failures

**Strategy**: Rolling Deployment with Sophisticated Health Checks + PodDisruptionBudget

**Infrastructure**: Kubernetes with comprehensive health checks (startup, liveness, readiness), PodDisruptionBudget

---

## Example 4: SaaS Platform - Feature Flag Deployment

[See SKILL.md Example 4 for complete details]

**Summary**: B2B SaaS platform with diverse customer requirements, using feature flags to decouple deployment from feature release, enabling targeted rollouts and instant feature rollback without redeployment.

**Key Highlights**:
- Deployment frequency: Multiple times per day
- Feature release frequency: Independent of deployments
- Zero production incidents from new features
- Instant feature rollback (< 1 second)
- Successful A/B testing program
- Different feature sets per customer tier
- Complete audit trail of feature changes

**Strategy**: Continuous Deployment + Feature Flags (LaunchDarkly)

**Infrastructure**: LaunchDarkly feature flag platform, gradual rollout automation, customer segmentation

---

## Additional Example Scenarios

### Example 5: Microservices Platform - Multi-Region Blue/Green

**Context**: Global microservices platform serving users across 5 continents needs to deploy updates across multiple regions while maintaining regional failover capabilities and minimizing cross-region latency.

**Requirements**:
- Deploy to multiple regions (US-East, US-West, EU, APAC, SA)
- Maintain regional failover during deployments
- Minimize cross-region latency
- Support region-specific rollback
- Coordinate deployments across regions
- Zero downtime globally

**Solution Design**:

**Selected Strategy**: Multi-Region Blue/Green with Staggered Rollout

**Implementation**:

1. **Region Deployment Order**:
```markdown
## Deployment Sequence

1. **Wave 1**: SA (lowest traffic, 5% of users)
   - Deploy to green environment
   - Monitor for 2 hours
   - Cutover if successful

2. **Wave 2**: APAC (15% of users)
   - Deploy to green environment
   - Monitor for 1 hour
   - Cutover if successful

3. **Wave 3**: EU (25% of users)
   - Deploy to green environment
   - Monitor for 1 hour
   - Cutover if successful

4. **Wave 4**: US-West (20% of users)
   - Deploy to green environment
   - Monitor for 1 hour
   - Cutover if successful

5. **Wave 5**: US-East (35% of users, highest traffic)
   - Deploy to green environment
   - Monitor for 2 hours
   - Cutover if successful
```

2. **Regional Deployment Automation**:
```python
# multi_region_deployment.py
import boto3
import time
from typing import List, Dict

class MultiRegionDeployment:
    def __init__(self, regions: List[str]):
        self.regions = regions
        self.region_clients = {
            region: boto3.client('elbv2', region_name=region)
            for region in regions
        }
    
    def deploy_all_regions(self, new_version: str):
        """
        Deploy to all regions in sequence
        """
        deployment_order = [
            {'region': 'sa-east-1', 'monitor_duration': 7200},
            {'region': 'ap-southeast-1', 'monitor_duration': 3600},
            {'region': 'eu-west-1', 'monitor_duration': 3600},
            {'region': 'us-west-2', 'monitor_duration': 3600},
            {'region': 'us-east-1', 'monitor_duration': 7200},
        ]
        
        for wave in deployment_order:
            region = wave['region']
            duration = wave['monitor_duration']
            
            print(f"Deploying to {region}")
            
            # Deploy to green environment
            self.deploy_to_green(region, new_version)
            
            # Validate deployment
            if not self.validate_deployment(region):
                print(f"Validation failed in {region}, aborting")
                self.rollback_region(region)
                return False
            
            # Cutover
            self.cutover_region(region)
            
            # Monitor
            if not self.monitor_region(region, duration):
                print(f"Monitoring failed in {region}, rolling back")
                self.rollback_region(region)
                return False
            
            print(f"Deployment to {region} successful")
        
        return True
    
    def deploy_to_green(self, region: str, version: str):
        """Deploy to green environment in specific region"""
        # Implementation for deploying to green environment
        pass
    
    def cutover_region(self, region: str):
        """Switch traffic to green environment in region"""
        client = self.region_clients[region]
        
        # Get green target group
        target_groups = client.describe_target_groups(
            Names=[f'myapp-green-{region}']
        )
        green_tg_arn = target_groups['TargetGroups'][0]['TargetGroupArn']
        
        # Update listener
        client.modify_listener(
            ListenerArn=self.get_listener_arn(region),
            DefaultActions=[{
                'Type': 'forward',
                'TargetGroupArn': green_tg_arn
            }]
        )
    
    def monitor_region(self, region: str, duration: int) -> bool:
        """Monitor region after cutover"""
        start_time = time.time()
        
        while time.time() - start_time < duration:
            metrics = self.get_region_metrics(region)
            
            if metrics['error_rate'] > 0.01:
                return False
            
            if metrics['p95_latency'] > 500:
                return False
            
            time.sleep(60)
        
        return True
```

**Results**:
- Zero global outages during deployments
- Regional rollback capability preserved
- Deployment time: 12-16 hours for all regions
- Early issue detection in low-traffic regions
- Reduced blast radius per region

---

### Example 6: Legacy Monolith - Strangler Fig Pattern Deployment

**Context**: Large legacy monolith application being gradually migrated to microservices needs deployment strategy that supports both old and new architectures during transition period.

**Requirements**:
- Support gradual migration from monolith to microservices
- Route traffic between monolith and microservices
- Enable independent deployment of microservices
- Maintain monolith deployment capability
- Zero downtime during migration
- Rollback to monolith if needed

**Solution Design**:

**Selected Strategy**: Strangler Fig Pattern with API Gateway Routing

**Implementation**:

1. **API Gateway Routing Configuration**:
```yaml
# api-gateway-routes.yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: app-routing
spec:
  hosts:
    - app.example.com
  http:
    # Route /users to new microservice
    - match:
        - uri:
            prefix: /api/users
      route:
        - destination:
            host: user-service
          weight: 100
    
    # Route /orders to new microservice
    - match:
        - uri:
            prefix: /api/orders
      route:
        - destination:
            host: order-service
          weight: 100
    
    # Route everything else to monolith
    - route:
        - destination:
            host: legacy-monolith
          weight: 100
```

2. **Gradual Migration Strategy**:
```markdown
## Migration Phases

### Phase 1: User Service Migration
- Extract user service from monolith
- Deploy user microservice
- Route 10% of /api/users traffic to microservice
- Gradually increase to 100%
- Decommission user code from monolith

### Phase 2: Order Service Migration
- Extract order service from monolith
- Deploy order microservice
- Route 10% of /api/orders traffic to microservice
- Gradually increase to 100%
- Decommission order code from monolith

### Phase 3-N: Continue for other services
```

3. **Deployment Procedure**:
```python
# strangler_deployment.py
class StranglerDeployment:
    def migrate_service(self, service_name: str, endpoint: str):
        """
        Migrate a service from monolith to microservice
        """
        # Deploy microservice
        self.deploy_microservice(service_name)
        
        # Gradually route traffic
        for percentage in [10, 25, 50, 75, 100]:
            self.update_routing(endpoint, percentage)
            
            if not self.monitor_migration(service_name, 600):
                print(f"Migration failed, rolling back")
                self.update_routing(endpoint, 0)
                return False
        
        # Decommission from monolith
        self.remove_from_monolith(service_name)
        return True
    
    def update_routing(self, endpoint: str, microservice_percentage: int):
        """
        Update API gateway routing
        """
        monolith_percentage = 100 - microservice_percentage
        
        routing_config = {
            "routes": [
                {
                    "match": {"prefix": endpoint},
                    "route": [
                        {
                            "destination": "microservice",
                            "weight": microservice_percentage
                        },
                        {
                            "destination": "monolith",
                            "weight": monolith_percentage
                        }
                    ]
                }
            ]
        }
        
        # Apply routing configuration
        self.apply_routing(routing_config)
```

**Results**:
- Gradual migration with zero downtime
- Independent microservice deployments
- Rollback capability to monolith
- Reduced migration risk
- Continuous delivery during migration

---

### Example 7: IoT Platform - Edge Device Deployment

**Context**: IoT platform with 100,000 edge devices needs to deploy firmware updates and application updates to devices in the field with limited connectivity and varying device capabilities.

**Requirements**:
- Deploy to 100,000 geographically distributed devices
- Handle intermittent connectivity
- Support different device hardware versions
- Minimize bandwidth usage
- Enable device-level rollback
- Monitor deployment progress
- Support emergency stop

**Solution Design**:

**Selected Strategy**: Phased Rollout with Device Cohorts + Delta Updates

**Implementation**:

1. **Device Cohort Strategy**:
```markdown
## Device Cohorts

### Cohort 1: Canary Devices (100 devices, 0.1%)
- Internal test devices
- Early adopter customers
- Duration: 24 hours

### Cohort 2: Beta Devices (1,000 devices, 1%)
- Beta program participants
- Diverse hardware versions
- Duration: 48 hours

### Cohort 3: Regional Rollout (10,000 devices, 10%)
- One geographic region
- Monitor regional performance
- Duration: 72 hours

### Cohort 4: Full Rollout (88,900 devices, 88.9%)
- All remaining devices
- Gradual over 7 days
```

2. **Deployment Orchestration**:
```python
# iot_deployment.py
import time
from typing import List

class IoTDeployment:
    def __init__(self, update_version: str):
        self.update_version = update_version
        self.deployed_devices = set()
        self.failed_devices = set()
    
    def deploy_to_cohorts(self):
        """
        Deploy to device cohorts sequentially
        """
        cohorts = [
            {'name': 'canary', 'size': 100, 'duration': 86400},
            {'name': 'beta', 'size': 1000, 'duration': 172800},
            {'name': 'regional', 'size': 10000, 'duration': 259200},
            {'name': 'full', 'size': 88900, 'duration': 604800},
        ]
        
        for cohort in cohorts:
            print(f"Deploying to {cohort['name']} cohort")
            
            devices = self.select_devices_for_cohort(cohort)
            
            # Deploy to cohort
            self.deploy_to_devices(devices)
            
            # Monitor cohort
            if not self.monitor_cohort(cohort, cohort['duration']):
                print(f"Cohort {cohort['name']} failed, stopping rollout")
                self.rollback_cohort(devices)
                return False
            
            print(f"Cohort {cohort['name']} successful")
        
        return True
    
    def deploy_to_devices(self, devices: List[str]):
        """
        Deploy update to specific devices
        """
        for device_id in devices:
            # Send update notification to device
            self.notify_device_update(device_id, self.update_version)
            
            # Device will pull update when connected
            # Track deployment status
    
    def monitor_cohort(self, cohort: dict, duration: int) -> bool:
        """
        Monitor cohort deployment
        """
        start_time = time.time()
        
        while time.time() - start_time < duration:
            metrics = self.get_cohort_metrics(cohort['name'])
            
            # Check success rate
            if metrics['success_rate'] < 0.95:
                print(f"Success rate {metrics['success_rate']} below threshold")
                return False
            
            # Check device health
            if metrics['device_health_score'] < 0.90:
                print(f"Device health score {metrics['device_health_score']} below threshold")
                return False
            
            time.sleep(3600)  # Check hourly
        
        return True
    
    def rollback_cohort(self, devices: List[str]):
        """
        Rollback devices in cohort
        """
        for device_id in devices:
            # Send rollback command
            self.send_rollback_command(device_id)
```

3. **Delta Update Optimization**:
```python
# delta_update.py
class DeltaUpdateGenerator:
    def generate_delta(self, old_version: str, new_version: str) -> bytes:
        """
        Generate delta update (only changes)
        """
        old_firmware = self.load_firmware(old_version)
        new_firmware = self.load_firmware(new_version)
        
        # Binary diff
        delta = self.binary_diff(old_firmware, new_firmware)
        
        # Compress delta
        compressed_delta = self.compress(delta)
        
        return compressed_delta
    
    def apply_delta(self, device_id: str, delta: bytes):
        """
        Apply delta update on device
        """
        current_firmware = self.get_device_firmware(device_id)
        
        # Apply binary patch
        new_firmware = self.apply_patch(current_firmware, delta)
        
        # Verify checksum
        if not self.verify_checksum(new_firmware):
            raise Exception("Checksum verification failed")
        
        # Install new firmware
        self.install_firmware(device_id, new_firmware)
```

**Results**:
- 100,000 devices updated in 14 days
- 98.5% success rate
- Bandwidth reduced by 70% using delta updates
- Early issue detection in canary cohort
- Zero bricked devices
- Emergency stop capability preserved

---

### Example 8: Database-Heavy Application - Schema Migration Deployment

**Context**: Application with complex database schema needs frequent deployments with schema changes while maintaining zero downtime and data integrity.

**Requirements**:
- Zero downtime deployments
- Support schema migrations
- Maintain data integrity
- Support rollback with data preservation
- Handle large database (10TB+)
- Minimize migration time

**Solution Design**:

**Selected Strategy**: Expand-Contract Pattern with Blue/Green Application Deployment

**Implementation**:

1. **Expand-Contract Migration Pattern**:
```markdown
## Three-Phase Migration

### Phase 1: Expand (Add new schema)
- Add new columns/tables
- Keep old columns/tables
- Both versions work
- Deploy new application version (blue/green)

### Phase 2: Migrate Data
- Copy data from old to new schema
- Dual-write to both schemas
- Verify data consistency

### Phase 3: Contract (Remove old schema)
- Stop writing to old schema
- Remove old columns/tables
- Clean up migration code
```

2. **Migration Implementation**:
```sql
-- Phase 1: Expand
-- Add new column (backward compatible)
ALTER TABLE users ADD COLUMN email_verified_v2 BOOLEAN DEFAULT FALSE;

-- Create trigger for dual-write
CREATE OR REPLACE FUNCTION sync_email_verified()
RETURNS TRIGGER AS $$
BEGIN
    -- Sync old column to new column
    NEW.email_verified_v2 := NEW.email_verified;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER sync_email_verified_trigger
BEFORE INSERT OR UPDATE ON users
FOR EACH ROW
EXECUTE FUNCTION sync_email_verified();

-- Phase 2: Migrate existing data
UPDATE users 
SET email_verified_v2 = email_verified
WHERE email_verified_v2 IS NULL;

-- Phase 3: Contract (next deployment)
-- ALTER TABLE users DROP COLUMN email_verified;
-- ALTER TABLE users RENAME COLUMN email_verified_v2 TO email_verified;
```

3. **Application Compatibility**:
```python
# user_model.py
class User:
    def __init__(self, data):
        # Support both old and new schema
        self.email_verified = (
            data.get('email_verified_v2') or 
            data.get('email_verified')
        )
    
    def save(self):
        # Write to both columns during migration
        data = {
            'email_verified': self.email_verified,
            'email_verified_v2': self.email_verified
        }
        db.update(data)
```

4. **Deployment Procedure**:
```markdown
## Deployment Procedure

### Week 1: Expand
1. Deploy schema expansion (add new columns)
2. Deploy application v2 (reads/writes both schemas)
3. Monitor for issues
4. Start data migration

### Week 2: Migrate
1. Continue data migration
2. Verify data consistency
3. Monitor application performance

### Week 3: Contract
1. Deploy application v3 (uses only new schema)
2. Verify application works
3. Drop old schema
4. Clean up migration code
```

**Results**:
- Zero downtime deployments with schema changes
- Data integrity maintained
- Rollback capability preserved
- Migration time: 3 weeks for major changes
- No data loss
- Backward compatibility during migration

---

## Comparison Matrix

| Example | Strategy | Downtime | Rollback Time | Complexity | Infrastructure Cost | Best For |
|---------|----------|----------|---------------|------------|---------------------|----------|
| 1. E-Commerce | Canary + Istio | Zero | 2 min | High | Medium | High-traffic microservices |
| 2. Banking | Blue/Green | 30 sec | 30 sec | Medium | High (2x) | Mission-critical, compliance |
| 3. Mobile API | Rolling | Zero | 8-10 min | Low | Low | Cost-sensitive, frequent deploys |
| 4. SaaS | Feature Flags | Zero | < 1 sec | Medium | Low | B2B, experimentation |
| 5. Multi-Region | Blue/Green | Zero | 5 min | High | High | Global applications |
| 6. Legacy Migration | Strangler Fig | Zero | 10 min | High | Medium | Monolith to microservices |
| 7. IoT | Phased Rollout | N/A | Varies | High | Low | Edge devices, IoT |
| 8. Database-Heavy | Expand-Contract | Zero | 1 week | High | Low | Schema migrations |

## Key Learnings Across Examples

### Common Success Patterns

1. **Gradual Rollout**: All successful strategies use some form of gradual rollout
2. **Automated Monitoring**: Automated monitoring and rollback is critical
3. **Health Checks**: Comprehensive health checks prevent routing to unhealthy instances
4. **Clear Rollback**: Well-defined rollback procedures and triggers
5. **Testing**: Thorough testing in non-production before production deployment
6. **Documentation**: Clear runbooks and procedures for deployment and rollback

### Common Challenges and Solutions

1. **Challenge**: Database schema changes
   - **Solution**: Expand-contract pattern, backward-compatible migrations

2. **Challenge**: Session management during deployment
   - **Solution**: Externalize sessions (Redis), use stateless authentication (JWT)

3. **Challenge**: High infrastructure costs
   - **Solution**: Use rolling or canary instead of blue/green, optimize resource usage

4. **Challenge**: Complex traffic routing
   - **Solution**: Use service mesh (Istio, Linkerd) or API gateway

5. **Challenge**: Monitoring and metrics
   - **Solution**: Implement comprehensive monitoring before deployment strategy

6. **Challenge**: Team adoption
   - **Solution**: Training, documentation, gradual rollout of deployment strategy itself

7. **Challenge**: Rollback with data changes
   - **Solution**: Design backward-compatible data changes, use expand-contract pattern

8. **Challenge**: Multi-region coordination
   - **Solution**: Staggered rollout, region-specific rollback capability