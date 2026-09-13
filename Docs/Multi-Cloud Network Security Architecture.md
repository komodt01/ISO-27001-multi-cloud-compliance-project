# Multi-Cloud Network Security Architecture

## Purpose

This document examines how a common network security requirement can be implemented across AWS, Microsoft Azure, and Google Cloud without assuming that each provider requires the same technical design.

The original version of this project attempted to create similar load balancer and next-generation firewall patterns in all three clouds. That approach made the implementations look consistent, but it did not account for important differences in each provider's networking and security services.

For this scenario, I would start with the required security outcome rather than a specific firewall product or load-balancing pattern.

The architecture question is:

**What traffic needs to be controlled or inspected, where should enforcement occur, and which cloud-native or third-party capability best satisfies that requirement?**

---

## Business Requirement

Assume the organization operates workloads across AWS, Azure, and Google Cloud and requires consistent network security expectations.

Those expectations include:

- Restrict unauthorized network communication
- Protect internet-facing workloads
- Control traffic between security zones
- Provide inspection where required by risk
- Protect administrative paths
- Maintain network visibility
- Support resilient business services
- Produce useful security evidence

The organization does not require every cloud to use the same firewall product or topology.

It requires consistent **security outcomes**.

---

## Architecture Decision Flow

For network security, I would use the following decision process:

**Business Requirement → Traffic Flow → Trust Boundary → Risk → Required Enforcement → Cloud Implementation → Telemetry → Response**

Before selecting a firewall or load balancer, I would determine:

- What is communicating?
- Where is the traffic coming from?
- Where is it going?
- Is the traffic north-south or east-west?
- Which trust boundary is being crossed?
- Is inspection required?
- Is simple filtering sufficient?
- Does the application require Layer 7 protection?
- Does the traffic contain sensitive information?
- What happens if the enforcement point fails?
- What telemetry is required?
- Who owns the control?

This prevents the architecture from beginning with a product and then forcing workloads through it.

---

# Common Security Model

At a high level, the architecture follows:

**Traffic Source → Network Enforcement / Inspection → Approved Destination**

Depending on the traffic and risk, the enforcement layer may include:

- Network firewall rules
- Security groups or equivalent workload controls
- Cloud-native managed firewalls
- Web application firewalls
- Third-party NGFW appliances
- Private connectivity
- Routing controls
- Load balancing
- Network segmentation
- IDS/IPS or network detection capabilities

Not every traffic flow requires every control.

---

# AWS Architecture

AWS provides several possible enforcement layers.

Depending on the requirement, I would evaluate capabilities such as:

- Security Groups
- Network ACLs
- AWS Network Firewall
- AWS WAF
- Gateway Load Balancer
- Application or Network Load Balancers
- Transit Gateway routing
- VPC segmentation
- Private connectivity
- Third-party firewall appliances

## Third-Party Inspection

If the organization requires a third-party NGFW such as Palo Alto Networks VM-Series, the architecture needs to account for more than simply placing firewall EC2 instances behind a Network Load Balancer.

I would evaluate:

- Required traffic path
- Appliance integration model
- Symmetric routing requirements
- Availability across zones
- Health checking
- Scaling
- Failure behavior
- Management interfaces
- Logging
- Licensing
- Software updates
- Inspection capacity
- Routing changes during failure

For transparent appliance insertion, AWS Gateway Load Balancer may be more appropriate than attempting to treat a firewall like an ordinary application backend.

The correct design depends on the firewall platform and traffic flow.

---

# Microsoft Azure Architecture

Azure should not be forced into the same topology used in AWS.

Potential capabilities include:

- Network Security Groups
- Azure Firewall
- Azure Web Application Firewall
- Azure Load Balancer
- Application Gateway
- Virtual WAN
- Route tables
- Private Link
- Virtual network segmentation
- Third-party network virtual appliances

## Azure Firewall

Azure Firewall is a managed firewall service.

I would treat it as a cloud-native network security capability rather than model it as a pair of ordinary firewall virtual machines placed behind a load balancer.

The architecture would instead evaluate:

- Which networks require inspection
- Routing through the firewall
- Firewall policy
- Application and network rules
- Threat intelligence capabilities where appropriate
- DNS considerations
- Logging
- Availability requirements
- Egress control
- Administrative ownership

## Third-Party NGFW

If a business requirement calls for a third-party firewall instead, the design changes.

A network virtual appliance architecture would need to evaluate:

- Vendor-supported Azure topology
- Load balancing requirements
- Routing
- High availability
- Health probes
- Failure behavior
- Management access
- Scale
- Licensing
- Logging

The important point is that **Azure Firewall and a third-party NGFW appliance are different architecture choices**.

They should not be treated as interchangeable implementations.

---

# Google Cloud Architecture

Google Cloud also provides multiple network enforcement options.

Depending on the requirement, I would evaluate:

- VPC firewall rules
- Hierarchical firewall policies
- Cloud NGFW
- Cloud Armor
- Load balancing
- Private Service Connect
- VPC segmentation
- Cloud Router and routing controls
- Third-party NGFW appliances
- Network telemetry

## Third-Party Inspection

If a third-party NGFW is required, a managed instance group may be one component of the design, but it is not the entire architecture.

I would also need to evaluate:

- Supported vendor deployment model
- Traffic steering
- Routing
- Health checks
- Availability zones
- Scaling behavior
- Inspection capacity
- Management connectivity
- Logging
- Software lifecycle
- Failure behavior

A generic HTTPS load-balancing configuration should not automatically be assumed to provide the required firewall inspection path.

---

# Workload-Level Controls vs. Central Inspection

One architecture decision is whether traffic should be controlled primarily through distributed workload-level controls or centralized inspection.

## Distributed Controls

Examples include:

- AWS Security Groups
- Azure Network Security Groups
- Google Cloud VPC firewall policies

Advantages can include:

- Enforcement close to the workload
- Cloud-native integration
- Reduced dependency on centralized appliances
- Fine-grained segmentation

Challenges can include:

- Policy consistency
- Rule sprawl
- Governance across many environments
- Visibility across distributed controls

## Centralized Inspection

Examples could include:

- Managed cloud firewalls
- Third-party NGFW platforms
- Central network hubs

Advantages can include:

- Centralized policy
- Deep inspection
- Consistent inspection capabilities
- Consolidated visibility

Challenges can include:

- Routing complexity
- Cost
- Latency
- Scaling
- Failure domains
- Operational dependencies

For this scenario, I would not assume one approach is universally better.

The decision depends on the traffic, risk, organizational operating model, and required inspection capabilities.

---

# North-South and East-West Traffic

I would evaluate these traffic patterns separately.

## North-South

Examples:

**Internet → Application**

or

**Application → Internet**

Potential controls include:

- DDoS protection
- Load balancing
- WAF
- Firewall
- TLS
- Egress filtering
- DNS controls
- Threat detection

## East-West

Examples:

**Application Tier → Database**

**Workload → Shared Service**

**VPC/VNet → VPC/VNet**

Potential controls include:

- Segmentation
- Workload identity
- Security groups or equivalent controls
- Routing
- Firewall policy
- Private connectivity
- Service-to-service authorization
- Network telemetry

A control appropriate for internet ingress is not automatically appropriate for internal service communication.

---

# Administrative Access

Administrative connectivity should be treated separately from application traffic.

For production environments, I would evaluate:

- Private administrative paths
- Bastion or controlled management services
- Identity-aware access
- MFA
- Privileged access management
- Just-in-time access
- Restricted source networks
- Session logging
- Administrative telemetry

Direct administrative access from the public internet should not be the default design.

---

# Telemetry and Monitoring

Network enforcement without visibility makes investigation and control validation difficult.

Depending on the architecture, useful telemetry could include:

- Firewall logs
- Flow logs
- WAF events
- Load balancer logs
- DNS activity
- Administrative changes
- Route changes
- Security policy changes
- Denied connections
- Unexpected traffic sources
- IDS/IPS findings
- Health-check failures

The monitoring flow becomes:

**Network Activity → Telemetry → Detection → Investigation → Response → Evidence**

Not every network event needs to become an alert.

Detection logic should focus on activity that represents meaningful security or operational risk.

---

# Failure Paths

Network security controls can become business-critical dependencies.

For that reason, I would explicitly evaluate failure behavior.

## Firewall Failure

Questions include:

- Does traffic fail open or fail closed?
- What is the business impact?
- Is another inspection instance available?
- Can traffic be rerouted?
- How is failure detected?
- Who is notified?

## Routing Failure

A valid firewall configuration does not help if traffic bypasses the intended enforcement point.

Routing and effective network paths need to be validated.

## Policy Error

An overly restrictive rule may interrupt legitimate business traffic.

The organization needs:

- Change control
- Testing
- Rollback
- Exception handling
- Ownership
- Monitoring

## Telemetry Failure

The network control may continue operating even though its logs are no longer reaching the monitoring platform.

Telemetry health should therefore be monitored independently from firewall health.

---

# Resilience

A resilient network security architecture may require:

- Multiple availability zones
- Redundant enforcement points
- Health monitoring
- Tested failover
- Capacity planning
- Controlled scaling
- Configuration synchronization
- Recovery procedures

Redundancy alone does not establish resilience.

The organization needs to understand how the complete traffic path behaves during failure.

---

# Security Evidence

Evidence that the architecture is operating as intended could include:

- Approved network architecture
- Firewall policies
- Security group or NSG rules
- Routing configuration
- Flow logs
- Firewall logs
- WAF logs
- Administrative activity
- Change records
- Alert history
- Incident records
- Rule reviews
- Exception approvals
- Failover test results

Evidence requirements should follow the organization's security, risk, and compliance requirements.

---

# ISO 27001 Context

Network security capabilities can support an ISO 27001-aligned security program through areas such as:

- Network security
- Network service security
- Segregation
- Access control
- Logging
- Monitoring
- Configuration management
- Change management
- Availability
- Security incident management

However, deploying a firewall, load balancer, or network security service does not by itself establish compliance.

The actual relationship depends on the organization's:

- ISMS scope
- Risk assessment
- Statement of Applicability
- Policies
- Architecture
- Control design
- Ownership
- Procedures
- Evidence
- Exceptions
- Control effectiveness

---

# Architecture Tradeoffs

For this scenario, some of the major tradeoffs I would evaluate are:

| Decision | Tradeoff |
|---|---|
| Cloud-native firewall vs. third-party NGFW | Native integration and operational simplicity vs. specialized inspection and enterprise standardization |
| Distributed controls vs. centralized inspection | Workload-level granularity vs. centralized policy and visibility |
| Deep inspection vs. performance | Greater visibility vs. latency and processing cost |
| Centralized routing vs. direct connectivity | Stronger enforcement consistency vs. complexity and dependency |
| Fail closed vs. fail open | Security enforcement vs. business availability |
| Extensive logging vs. selective telemetry | Investigation depth vs. ingestion, storage, and operational cost |

There is no single answer that is correct for every workload.

---

# Architecture Takeaway

The original question should not be:

**“How do I deploy the same firewall architecture in AWS, Azure, and Google Cloud?”**

For this scenario, I would instead ask:

**“Which traffic requires protection or inspection, what risk are we addressing, and which enforcement capability makes sense in each cloud?”**

That produces the architecture flow:

**Business Requirement → Traffic Flow → Trust Boundary → Risk → Enforcement Requirement → Cloud-Specific Implementation → Telemetry → Response**

The three cloud environments may use different technical designs while still supporting the same enterprise security objectives.

**Consistent network security outcomes do not require identical network architectures.**