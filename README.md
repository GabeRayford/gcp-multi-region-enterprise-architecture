Phase 1: Core Global Network Foundations
📋 Real-World Operational Scenario
• The Business Challenge: An enterprise financial technology platform requires a highly resilient, zero-trust network infrastructure capable of sustaining a total regional data center blackout without dropping customer transactions. To prevent lateral movement and data exfiltration, corporate compliance mandates that all internal application tiers must be completely isolated from direct internet exposure, while still allowing secure egress for software dependency updates.
• The Technical Resolution: Initialized a custom-mode global VPC network matrix traversing decoupled subnets across us-central1 and us-east4 to eliminate default automated range pooling. Enforced the isolation of the internal application workloads by enabling Private Google Access across all regional subnets. Applied a zero-trust ingress security profile utilizing a strict firewall layout that blocks all public traffic, explicitly restricting network access to authorized Google Global Load Balancer health checkers.


# 1. Establish project environment target context safely
export MY_PROJ="project-c1a05de0-ba4b-4764-93a"

# 2. Initialize the global custom-subnet mode corporate network matrix
gcloud compute networks create enterprise-global-vpc --subnet-mode=custom --bgp-routing-mode=global --project=$MY_PROJ

# 3. Provision the isolated Primary Subnet (Region 1 - US Central) with Private Google Access enabled
gcloud compute networks subnets create prod-central-app-subnet --network=enterprise-global-vpc --region=us-central1 --range=10.10.10.0/24 --enable-private-ip-google-access --project=$MY_PROJ

# 4. Provision the isolated Failover Subnet (Region 2 - US East) with Private Google Access enabled
gcloud compute networks subnets create prod-east-app-subnet --network=enterprise-global-vpc --region=us-east4 --range=10.20.10.0/24 --enable-private-ip-google-access --project=$MY_PROJ

# 5. Apply the zero-trust ingress security profile to isolate application traffic to proxy health checks only
gcloud compute firewall-rules create allow-glb-health-checks --network=enterprise-global-vpc --allow=tcp:80,tcp:443 --source-ranges=35.191.0.0/16,130.211.0.0/22 --description="Isolate application traffic to proxy health checks only" --project=$MY_PROJ
Use code with caution.
🔍 Validation Protocol
text
NAME: allow-glb-health-checks
NETWORK: enterprise-global-vpc
DIRECTION: INGRESS
PRIORITY: 1000
ALLOW: tcp:80,tcp:443
DENY: 
DISABLED: False
