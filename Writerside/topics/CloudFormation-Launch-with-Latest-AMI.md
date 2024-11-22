# CloudFormation Launch with Latest AMI

You can use `SSM Parameters` from `SSM Parameter Store` directly in the template:
```yaml
# Use public Systems Manager Parameter
Parameters:
  LatestAmiId:
    Type: 'AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>'
    Default: '/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2'

Resources:
 Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      ImageId: !Ref LatestAmiId
```