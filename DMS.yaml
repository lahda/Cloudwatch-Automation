AWSTemplateFormatVersion: '2010-09-09'
Description: CloudFormation Template for AWS Database Migration Workshop.

Metadata:
  AWS::CloudFormation::Interface:
    ParameterGroups:
      - Label:
          default: "Database Migration Workshop Lab Environment"
        Parameters:
          - LabType
      - Label:
          default: "Amazon EC2 Configuration"
        Parameters:
          - EC2ServerInstanceType
          - KeyName
      - Label:
          default: "Target Amazon RDS Database Configuration"
        Parameters:
          - RDSInstanceType
      - Label:
          default: "Network Configuration"
        Parameters:
          - VpcCIDR

Resources:
  DMSMigrationBucket:
    Type: 'AWS::S3::Bucket'
    Properties:
      VersioningConfiguration:
        Status: Enabled
      BucketName: !Sub 'dms-sc-${AWS::AccountId}-${AWS::StackName}'

  DmsVpc:
    Type: AWS::EC2::VPC
    Properties:
      EnableDnsSupport: 'true'
      EnableDnsHostnames: 'true'
      CidrBlock:
        Ref: VpcCIDR
      Tags:
      - Key: Name
        Value:
          Fn::Join:
          - "-"
          - - Ref: AWS::StackName
            - DmsVpc

  Subnet1:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref DmsVpc
      CidrBlock: !Select [ 0, !Cidr [ !GetAtt DmsVpc.CidrBlock, 3, 8 ]]
      AvailabilityZone: !Select [0, !GetAZs ]
      Tags:
      - Key: Name
        Value:
          Fn::Join:
          - "-"
          - - Ref: AWS::StackName
            - Subnet1

  Subnet2:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref DmsVpc
      CidrBlock: !Select [ 1, !Cidr [ !GetAtt DmsVpc.CidrBlock, 3, 8 ]]
      AvailabilityZone: !Select [1, !GetAZs ]
      Tags:
      - Key: Name
        Value:
          Fn::Join:
          - "-"
          - - Ref: AWS::StackName
            - Subnet2

  InternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
      - Key: Name
        Value:
          Fn::Join:
          - "-"
          - - Ref: AWS::StackName
            - InternetGateway

  AttachGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref DmsVpc
      InternetGatewayId: !Ref InternetGateway

Outputs:
  BucketName:
    Description: "Nom du bucket S3"
    Value: !Ref DMSMigrationBucket
  VpcId:
    Description: "ID du VPC"
    Value: !Ref DmsVpc
  Subnet1Id:
    Description: "ID du premier sous-réseau"
    Value: !Ref Subnet1
  Subnet2Id:
    Description: "ID du deuxième sous-réseau"
    Value: !Ref Subnet2
