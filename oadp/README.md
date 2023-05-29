# OpenShift API for Data Protection (OADP)

The OpenShift API for Data Protection (OADP) is an Operator that helps you define and configure installation of Velero and its required resources to run on OpenShift.

You can download and install the Velero CLI tool by following the instructions on the [Velero documentation page](https://velero.io/docs/v1.9/basic-install/#install-the-cli).

You must have the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) installed.


## Configuring AWS

Set the BUCKET variable:
```
BUCKET=<your_bucket>
```

Set the REGION variable:
```
REGION=<your_region>
```

Create an AWS S3 bucket:
```
aws s3api create-bucket \
    --bucket $BUCKET \
    --region $REGION \
    --create-bucket-configuration LocationConstraint=$REGION 
```

Create an IAM user:
```
aws iam create-user --user-name velero 
```

Create a velero-policy.json file:
```
cat > velero-policy.json <<EOF
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeVolumes",
                "ec2:DescribeSnapshots",
                "ec2:CreateTags",
                "ec2:CreateVolume",
                "ec2:CreateSnapshot",
                "ec2:DeleteSnapshot"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:DeleteObject",
                "s3:PutObject",
                "s3:AbortMultipartUpload",
                "s3:ListMultipartUploadParts"
            ],
            "Resource": [
                "arn:aws:s3:::${BUCKET}/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketLocation",
                "s3:ListBucketMultipartUploads"
            ],
            "Resource": [
                "arn:aws:s3:::${BUCKET}"
            ]
        }
    ]
}
EOF
```

Attach the policies to give the velero user the minimum necessary permissions:
```
aws iam put-user-policy \
  --user-name velero \
  --policy-name velero \
  --policy-document file://velero-policy.json
```

Create an access key for the velero user:
```
aws iam create-access-key --user-name velero
```

Create a credentials-velero file:
```
cat << EOF > ./credentials-velero
[default]
aws_access_key_id=<AWS_ACCESS_KEY_ID>
aws_secret_access_key=<AWS_SECRET_ACCESS_KEY>
EOF
```