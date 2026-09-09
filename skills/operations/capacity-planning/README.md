# Capacity Planning Skill

## Quick Reference

**Purpose**: Estimate and plan infrastructure capacity to meet current and future demand while optimizing costs, performance, and reliability.

**Complexity**: Advanced  
**Estimated Time**: 2-4 weeks for comprehensive capacity planning

## When to Use This Skill

✅ **Use this skill when**:
- Planning infrastructure for new applications or services
- Current infrastructure approaching capacity limits
- Experiencing performance degradation or bottlenecks
- Planning for business growth or geographic expansion
- Preparing for seasonal traffic spikes or major events
- Optimizing infrastructure costs
- Migrating to cloud or changing platforms
- Ensuring SLA compliance and performance targets

❌ **Don't use this skill when**:
- Responding to active incidents or outages
- Debugging application performance issues
- Fixing configuration problems
- Selecting vendors or technologies
- Only analyzing costs without capacity planning

## Key Inputs

1. **Current Infrastructure Inventory**: Complete list of compute, storage, network resources with utilization metrics
2. **Historical Usage Data**: Traffic patterns, resource utilization trends over 6-12 months
3. **Business Requirements**: Growth projections, SLA targets, compliance requirements
4. **Application Characteristics**: Resource consumption patterns, scaling characteristics
5. **Performance Metrics**: Response time, throughput, error rate requirements

## Key Outputs

1. **Capacity Plan Document**: Comprehensive plan with current state, projections, and recommendations
2. **Resource Sizing**: Specific recommendations for compute, storage, network, database capacity
3. **Growth Projections**: Forecasts over 6-month, 1-year, and 3-year horizons
4. **Cost Analysis**: Current and projected costs with optimization recommendations
5. **Implementation Roadmap**: Phased plan with timeline, milestones, and success criteria
6. **Monitoring Strategy**: Metrics, alerts, and dashboards for capacity tracking

## 10-Step Workflow

1. **Data Collection and Current State Assessment**: Inventory infrastructure, collect metrics, identify bottlenecks
2. **Business Requirements Analysis**: Gather growth projections, define SLAs, understand budget
3. **Workload Characterization**: Analyze resource consumption patterns, identify scaling characteristics
4. **Demand Forecasting**: Project future capacity needs using statistical methods
5. **Capacity Modeling**: Calculate required infrastructure capacity with headroom and redundancy
6. **Cost Analysis and Optimization**: Estimate costs and identify optimization opportunities
7. **Risk Assessment and Mitigation**: Identify capacity risks and develop mitigation strategies
8. **Implementation Planning**: Create phased roadmap with timeline and success criteria
9. **Monitoring and Alerting Design**: Define metrics, alerts, and dashboards
10. **Documentation and Knowledge Transfer**: Document plan and enable team execution

## Quick Decision Guide

### Headroom Strategy
- **Conservative (50-100%)**: High uncertainty, slow scaling, critical SLAs
- **Moderate (20-50%)**: Balanced approach, can scale in hours
- **Minimal (10-20%)**: Low uncertainty, fast auto-scaling

### Scaling Strategy
- **Vertical**: Stateful workloads, simpler management
- **Horizontal**: Stateless workloads, high availability needs
- **Auto-scaling**: Variable workload, cost optimization priority

### Forecasting Method
- **Time Series**: Sufficient historical data, clear trends
- **Regression**: Identifiable drivers, want scenario modeling
- **Scenario Planning**: High uncertainty, multiple trajectories

## Common Mistakes to Avoid

1. ❌ Insufficient historical data (< 6 months)
2. ❌ Ignoring seasonal patterns
3. ❌ Not accounting for business changes
4. ❌ Inadequate headroom for uncertainty
5. ❌ Forgetting redundancy requirements
6. ❌ Linear extrapolation of non-linear growth
7. ❌ Not updating forecasts regularly
8. ❌ Skipping testing and validation
9. ❌ Poor documentation
10. ❌ No continuous improvement process

## Success Metrics

**Capacity**:
- ✓ 20-50% headroom maintained
- ✓ 50-80% average utilization
- ✓ Meets SLA targets

**Performance**:
- ✓ Response time meets p95/p99 targets
- ✓ Supports required throughput
- ✓ < 0.1% capacity-related errors

**Cost**:
- ✓ Within 10% of budget
- ✓ Cost per user/transaction stable or decreasing
- ✓ 10-30% optimization savings achieved

**Business**:
- ✓ No capacity-related delays
- ✓ No revenue loss from capacity issues
- ✓ Customer satisfaction maintained

## Related Skills

**Prerequisites**:
- Monitoring and observability setup
- Infrastructure inventory
- Performance baselining

**Commonly Followed By**:
- Infrastructure provisioning
- Auto-scaling configuration
- Cost optimization
- Performance testing

**Works With**:
- Cloud migration
- Disaster recovery planning
- Architecture design
- Database optimization

## Resources

- **SKILL.md**: Comprehensive skill documentation with 4 detailed examples
- **instructions.md**: Step-by-step implementation guide
- **examples.md**: 8 diverse real-world examples

## Quick Start

1. **Assess Current State** (Week 1):
   - Inventory infrastructure
   - Collect 6-12 months of metrics
   - Identify current bottlenecks

2. **Gather Requirements** (Week 1):
   - Interview business stakeholders
   - Document growth projections
   - Define SLA requirements

3. **Forecast and Model** (Week 2):
   - Build demand forecast models
   - Calculate capacity requirements
   - Size infrastructure components

4. **Analyze and Plan** (Week 2-3):
   - Estimate costs
   - Assess risks
   - Create implementation roadmap

5. **Document and Execute** (Week 3-4):
   - Write capacity plan document
   - Set up monitoring
   - Begin phased implementation

## Tips for Success

💡 **Start with optimization**: Optimize existing infrastructure before adding capacity  
💡 **Use multiple scenarios**: Plan for conservative, expected, and aggressive growth  
💡 **Maintain headroom**: 30% headroom is a good starting point for most cases  
💡 **Monitor continuously**: Track actual vs. forecast to refine projections  
💡 **Update regularly**: Review and update capacity plan quarterly  
💡 **Document assumptions**: Make all assumptions explicit and validate with stakeholders  
💡 **Test thoroughly**: Validate capacity through load testing before production  
💡 **Plan for failure**: Include redundancy and disaster recovery capacity  

## Example Use Cases

1. **E-Commerce Holiday Planning**: Plan capacity for Black Friday/Cyber Monday traffic spikes
2. **SaaS Growth**: Scale database and application infrastructure for rapid user growth
3. **Global Expansion**: Size infrastructure for international market entry
4. **Product Launch**: Estimate capacity needs for new product or feature launch
5. **Cost Optimization**: Right-size infrastructure to reduce costs while meeting SLAs
6. **Cloud Migration**: Estimate cloud infrastructure requirements for migration
7. **Compliance**: Plan capacity to meet regulatory requirements and audit needs
8. **M&A Integration**: Size infrastructure for merger or acquisition integration

---

**Version**: 1.0.0  
**Category**: Operations  
**Last Updated**: 2026-09-09
