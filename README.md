<<h1>Architecture Diagram</h1>
<p align="center">
    <img src="docs/diagrams/multi_account_strategy_and_design.drawio.svg" width="900">
</p>

<h1>Multi-Account Strategy</h1>

<h3>Core OU</h3>
<p>Contains governance and security accounts for operating and controlling the AWS environment. Highly restricted and rarely change. Contains: </p>

<p>
<strong>Management Account</strong>
<ul>
    <li>Used to manage AWS Organizations, Service Control policies (SCPs), and Billing and cost management.</li>
    <li>No workloads should run in these accounts.</li>
    <li>Limit access.</li>
</ul>

<strong>AFT Management Account</strong>
<ul>
    <li>Dedicated account for Account Factory for Terraform.</li>
    <li>Separate from Management account to reduce blast reduce and improve security and auditability.</li>
</ul>

<strong>Log Archive Account</strong>
<ul>
    <li>Centralized storage for AWS CloudTrail logs, AWS Config logs, VPC Flow logs.</li>
    <li>Logs are immutable and write-only from other accounts.</li>
</ul>

<strong>Audit Account</strong>
<ul>
    <li>Used by security and compliance teams</li>
    <li>Read-only access across the organization for CloudTrail, AWS Config, Security Hub.</li>
</ul>
</p>

<hr>

<h3>Foundation OU</h3>
<p>Shared, low-level infrastructure that other accounts depend on</p>

<p>
<strong>Networking Account</strong>
<ul>
    <li>Centralized network management account for:</li>
    <ul>
        <li>Shared VPCs</li>
        <li>Transit Gateway</li>
        <li>VPNs</li>
        <li>Direct Connect</li>
        <li>Network firewalls</li>
        <li>Central routing and DNS</li>
    </ul>
    <li>Enables hub-and-spoke networking</li>
</ul>
</p>

<hr>


<h3>Automation OU</h3>
<p>Contains accounts responsible for platform automation and delivery pipelines.</p>

<p>
<strong>CI/CD Account</strong>
<ul>
    <li>Host centralized CI/CD tooling: </li>
    <ul>
        <li>CodePipeline</li>
        <li>CodeBuild</li>
        <li>GitHub Actions runners</li>
    </ul>
    <li>Deploys infrastructure and applications cross-account</li>
    <li>Uses IAM roles with least privilege</li>
</ul>

<strong>AFT Management Account</strong>
<ul>
    <li>In some designs, AFT is grouped under Automation OUs</li>
</ul>
</p>

<hr>

<h3>Security OU</h3>
<p>Contains accounts for security operations and threat detection</p>

<p>
<strong>Security Tooling Account</strong>
<ul>
    <li>Central security services account</li>
    <ul>Services include: 
        <li>AWS Security Hub</li>
        <li>GuardDuty (Delagated Admin) </li>
        <li>IAM Access Analyzer</li>
    </ul>
    <li>Aggregates findings from all accounts</li>
</ul>
</p>

<hr>

<h3>Shared Services</h3>
<p>Contains reusable platform services consumed by workloads</p>

<p>
<strong>Observability Account</strong>
<ul>
    <li>Central monitoring and logging includes: </li>
    <ul>
        <li>Amazon CloudWatch cross-account dashboards</li>
        <li>Prometheus / Grafana</li>
        <li>Central Alerting</li>
    </ul>
    <li>Improves visibility and reduces duplication</li>
</ul>

<strong>Shared ECR Account</strong>
<ul>
    <li>Central Amazon ECR registry</li>
    <li>Stores container images used bu multiple workloads</li>
    <ul>Enables: 
        <li>Image reuse</li>
        <li>Central scanning</li>
        <li>Cost Optimization</li>
    </ul>
</ul>
</p>

<hr>

<h3>Workloads OU</h3>
<p>Contains application accounts, fully isolated from each other</p>

<strong>Application-Specific OUs</strong>
<ul>
    <li>Each application is grouped into its own OU to allow Application-specific SCPs and independent scaling and governance</li>
    <li>Dev Account:
        <ul> 
            <li>Used for development and experimentation</li>
            <li>Fewer restrictions</li>
            <li>Faster iteration</li>
        </ul>
    </li>
    <li>Staging Account:
        <ul> 
            <li>Pre-production testing</li>
            <li>Mirrors production architecture</li>
            <li>Used for validation and QA</li>
        </ul>
    </li>
    <li>Prod Account:
        <ul> 
            <li>Production workloads</li>
            <li>Strict SCPs</li>
            <li>Highest security and compliance controls</li>
        </ul>
    </li>
</ul>

<hr>

<h3>Summary</h3>
<p>This design follows AWS guidance for: </p>

<ul>
    <li>Strong Isolation</li>
    <li>Centralized security and logging</li>
    <li>Separation of duties</li>
    <li>Scalable multi-account governance</li>
    <li>Infrastructure automation using AFT</li>
</ul>
<br>

<h1>Services Cost</h1>

<h3>CloudWatch</h3>
<ul>
    <li>If the requirement is central visibility without extra ingestion cost</li>
    <ul><li>Use CloudWatch cross-account obsevability and shared dashboards</li></ul>
    <li>If the requirement is central log repository for compliance or analytics</li>
    <ul><li>Use the new log centralization feature, first copy is free, backups cost extra</li></ul>
    <li>For raw archival or export</li>
    <ul><li>Export to S3 or use Firehose, storage and delivery charges applied</li></ul>
    <li>References</li>
    <ul>
        <li><a href="https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Unified-Cross-Account.html?utm_source=chatgpt.com">CloudWatch cross-account observability</a></li>
        <li><a href="https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CloudWatchLogs_Centralization.html?utm_source=chatgpt.com">Cross-account cross-Region log centralization</a></li>
    </ul>
</ul>
