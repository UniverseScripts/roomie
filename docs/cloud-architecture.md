graph TD;
    A["AWS Cloud"]
    A-->B["VPC"]
    A-->C["S3 Bucket"]
    A-->D["EC2 Instance"]
    A-->E["RDS"]
    A-->F["Lambda Function"]
    B-->G["Public Subnet"]
    B-->H["Private Subnet"]
    D-->I["Load Balancer"]
    I-->D
    I-->J["HTTPS"]