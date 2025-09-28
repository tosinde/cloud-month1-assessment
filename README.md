# cloud-month1-assessment
# CloudLaunch Infrastructure Setup

This repository documents the setup of **Task 1 (Static Website Hosting Using S3 + IAM User with Limited Permissions)** and **Task 2 (VPC Design for CloudLaunch Environment)**.


## Task 1: S3 Buckets & IAM User

### Buckets Created
1. **esthers-cloudlaunch-site-bucket**
   - Static website hosting enabled.
   - Public read-only access (anonymous users).
   - Static Site URL: https://esthers-cloudlaunch-site-bucket.s3.eu-north-1.amazonaws.com/static+site/ 

   CloudFront Distribution:  
   https://d203dknwuczio4.cloudfront.net  

2. **esthers-cloudlaunch-private-bucket**
   - Not publicly accessible.
   - Accessible only by `esthers-cloudlaunch-user` with `GetObject` and `PutObject` permissions.  
   - No `DeleteObject`.

3. **esthers-cloudlaunch-visible-only-bucket**
   - Not publicly accessible.
   - `esthers-cloudlaunch-user` can **list** the bucket but cannot read objects.


### IAM User: `esthers-cloudlaunch-user`
- Permissions restricted to only the 3 buckets above.  
- No delete permissions. 
- Enforced password reset on first login. 

#### IAM Policy JSON
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInternetGateways",
                "ec2:DescribeVpcs",
                "ec2:DescribeSubnets",
                "ec2:DescribeRouteTables",
                "ec2:DescribeSecurityGroups"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": [
                "arn:aws:s3:::esthers-cloudlaunch-site-bucket",
                "arn:aws:s3:::esthers-cloudlauch-private-bucket",
                "arn:aws:s3:::esthers-cloudlaunch-visible-only-bucket"
            ]
        },
        {
            "Sid": "VisualEditor2",
            "Effect": "Allow",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::esthers-cloudlauch-site-bucket/static site/"
        },
        {
            "Sid": "VisualEditor3",
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3:::esthers-cloudlaunch-private-bucket /*"
        },
        {
            "Sid": "VisualEditor4",
            "Effect": "Deny",
            "Action": "s3:DeleteObject",
            "Resource": [
                "arn:aws:s3:::esthers-cloudlaunch-private-bucket/*",
                "arn:aws:s3:::esthers-cloudlaunch-site-bucket/*",
                "arn:aws:s3:::esthers-cloudlaunch-visible-only-bucket/*"
            ]
        }
    ]
}
  ]
}


## AWS IAM Console Access (for Instructor)

- **Console URL:** (https://esteri.signin.aws.amazon.com/console)
- **IAM User:** esthers-cloudlaunch-user
- **Temporary Password:** will be provided to instructor privately
- **Note:** User is forced to reset password at first login.
