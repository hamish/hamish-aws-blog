---
title: 'Simple VPC'
tags:
  - 'AWS::IAM::Role'
  - 'AWS::EC2::VPC'
date: 2017-08-23T15:04:10.000Z
categories: 
  - 'CloudFormation'
menu: 'main'
banner: 'banners/placeholder.png'
description: >-
  Creates a simple VPC
---

Lorem ipsum dolor sit amet, consectetur adipiscing elit. In molestie non odio venenatis varius. Pellentesque at odio purus. Fusce quis egestas ipsum. Donec lacinia erat vel nunc mattis, in consectetur enim commodo. Phasellus viverra aliquam commodo. Aliquam aliquam sodales dui, vitae elementum metus finibus non. Mauris non molestie justo. Vivamus lobortis nulla nec eros scelerisque, ac hendrerit dui tempus. Etiam nunc est, rhoncus at porta a, elementum sit amet magna. Vestibulum interdum, est ultrices rhoncus finibus, augue urna lobortis velit, nec molestie ligula arcu vitae risus. Nullam pulvinar rutrum placerat.

Suspendisse cursus semper massa, eget sagittis justo dictum aliquet. Vivamus porttitor dictum interdum. Fusce fringilla blandit ligula id malesuada. Phasellus egestas a ligula et ultricies. Etiam ultrices diam eget placerat maximus. Ut et finibus felis, nec blandit est. Praesent commodo dictum convallis. Donec ac pretium nulla, ut interdum massa. Proin mattis nulla eros, in porttitor tellus molestie in. Aliquam placerat malesuada tellus in viverra. In eros risus, sollicitudin non malesuada vitae, pulvinar quis urna. Duis sit amet turpis lorem. Ut pulvinar urna pretium nulla blandit accumsan. Curabitur in viverra nisl, eget gravida ex. Donec mattis bibendum urna nec ultricies. Aliquam egestas placerat lorem.

Donec risus turpis, pharetra vitae velit quis, convallis venenatis tortor. Proin imperdiet gravida felis in consectetur. Vivamus vitae viverra elit. Suspendisse sed laoreet magna, vitae viverra nibh. Sed ut ultricies eros. Etiam eleifend luctus malesuada. Suspendisse in dolor id metus mattis molestie. Ut quis tellus ac libero placerat finibus. Vivamus placerat euismod lacinia. Integer aliquam, ante non scelerisque facilisis, ex nulla tristique nunc, non varius dolor lorem eget leo. Cras at est metus. Ut vehicula pellentesque turpis, eget tristique est. Pellentesque libero sapien, efficitur eu pellentesque vitae, elementum a metus. Cras luctus eros lacus, sed maximus sem aliquam nec.

Nunc iaculis, ipsum id tempor ullamcorper, neque arcu tristique leo, id gravida nisi ex quis ligula. Nulla facilisi. Aliquam sed ex mi. Sed facilisis tempor massa vitae semper. Nam id metus massa. Integer et aliquam ante. Sed sed fringilla magna. Vivamus vestibulum ipsum aliquam euismod rutrum. Suspendisse porta tincidunt massa, eget blandit sapien dictum vitae. Curabitur felis enim, vulputate et accumsan ut, pellentesque vel odio. Etiam tincidunt lobortis lorem, ut vehicula massa luctus vitae. Integer erat mauris, condimentum sit amet tempus at, pharetra vitae nibh. In in rhoncus tortor. Nulla fringilla dolor ut est faucibus, vitae volutpat turpis tempor. Nulla pulvinar turpis est, vel dapibus sem imperdiet ac. Fusce faucibus condimentum scelerisque.

Phasellus tellus velit, aliquet eget lacus eget, pretium tempor augue. Vestibulum eget diam faucibus, aliquam risus nec, elementum massa. Praesent ut dignissim mauris, a tincidunt metus. Vestibulum sollicitudin rutrum placerat. Donec pharetra, felis in elementum molestie, lorem massa fermentum augue, luctus tincidunt massa neque vitae nunc. Ut malesuada leo mi, vitae hendrerit elit efficitur vitae. Proin id quam at enim mattis rhoncus. Suspendisse lobortis eros et orci faucibus, et rhoncus augue luctus. Proin vulputate euismod nunc sit amet fermentum. Curabitur eget tellus rhoncus, interdum urna nec, dignissim neque. Aliquam lectus magna, varius id est et, mollis maximus turpis.

Praesent sapien enim, sagittis facilisis ullamcorper ut, ultrices vitae urna. Pellentesque nec dapibus nisi. Nulla varius varius est, vitae elementum turpis hendrerit finibus. Interdum et malesuada fames ac ante ipsum primis in faucibus. Vestibulum maximus ac nulla non consectetur. Vivamus commodo nunc vel finibus ullamcorper. Curabitur ut augue sed turpis lacinia efficitur. In volutpat orci tincidunt magna vulputate consequat.


    ---
    Description: Create IAM role, a server with that role and a tag and an SSM association
      that configures the server based upon the tag.
    Parameters:
      KeyPairName:
        Description: The keypair to use to launch instances with.
        NoEcho: false
        Type: String
    Resources:
      SSMSecurityGroup:
        Properties:
          GroupDescription: Security Group for SSMSecurityGroup
          SecurityGroupIngress:
          - CidrIp: 0.0.0.0/0
            FromPort: "-1"
            IpProtocol: icmp
            ToPort: "-1"
          - CidrIp: 0.0.0.0/0
            FromPort: '3389'
            IpProtocol: tcp
            ToPort: '3389'
          Tags:
          - Key: Name
            Value: SSMSecurityGroup
        Type: AWS::EC2::SecurityGroup
      ssm7:
        Properties:
          IamInstanceProfile:
            Ref: ssm7InstanceProfile
          ImageId: ami-bf8895dc
          KeyName:
            Ref: KeyPairName
          SecurityGroupIds:
          - Ref: SSMSecurityGroup
          Tags:
          - Key: Name
            Value: ssm7
          - Key: SSM_ASSOCIATION
            Value: ssm7
        Type: AWS::EC2::Instance
      ssm7InstanceProfile:
        Properties:
          Path: "/"
          Roles:
          - Ref: ssm7Role
        Type: AWS::IAM::InstanceProfile
      ssm7Role:
        Properties:
          AssumeRolePolicyDocument:
            Statement:
            - Action:
              - sts:AssumeRole
              Effect: Allow
              Principal:
                Service:
                - ec2.amazonaws.com
            Version: '2012-10-17'
          ManagedPolicyArns:
          - arn:aws:iam::aws:policy/service-role/AmazonEC2RoleforSSM
          Path: "/"
          Policies:
          - PolicyDocument:
              Statement:
              - Action:
                - ec2:DescribeTags
                Effect: Allow
                Resource:
                - "*"
                Sid: StmtTags
              - Action:
                - ec2:DescribeInstanceStatus
                - logs:CreateLogGroup
                - logs:CreateLogStream
                - logs:DescribeLogGroups
                - logs:DescribeLogStreams
                - logs:PutLogEvents
                - cloudwatch:PutMetricData
                Effect: Allow
                Resource:
                - "*"
                Sid: StmtCWLogs
              - Action:
                - s3:GetObject
                Effect: Allow
                Resource:
                - arn:aws:s3:::forcenet-release-packages/*
                Sid: AllowGetObjForcenetRelease
              - Action:
                - s3:ListBucket
                Effect: Allow
                Resource:
                - arn:aws:s3:::forcenet-release-packages
                Sid: AllowListForcenetRelease
              Version: '2012-10-17'
            PolicyName: ssm7ServerPolicy
        Type: AWS::IAM::Role
      ssmAssnssm7:
        Properties:
          Name: AWS-ConfigureCloudWatch
          Parameters:
            properties:
            - '{"IsEnabled": true, "EngineConfiguration": {"PollInterval": "00:00:15",
              "Flows": {"Flows": ["SSMLog,CloudWatchLogs", "(PerformanceCounterMemory,
              PerformanceCounterDisk),CloudWatchMetrics"]}, "Components": [{"FullName":
              "AWS.EC2.Windows.CloudWatch.PerformanceCounterComponent.PerformanceCounterInputComponent,AWS.EC2.Windows.CloudWatch",
              "Id": "PerformanceCounterMemory", "Parameters": {"CategoryName": "Memory",
              "DimensionName": "ByInstanceId", "CounterName": "Available MBytes", "DimensionValue":
              "ssm7 - {instance_id}", "InstanceName": "", "Unit": "Megabytes", "MetricName":
              "Memory"}}, {"FullName": "AWS.EC2.Windows.CloudWatch.PerformanceCounterComponent.PerformanceCounterInputComponent,AWS.EC2.Windows.CloudWatch",
              "Id": "PerformanceCounterDisk", "Parameters": {"CategoryName": "LogicalDisk",
              "DimensionName": "ByInstanceId", "CounterName": "Free Megabytes", "DimensionValue":
              "ssm7 - {instance_id}", "InstanceName": "C:", "Unit": "Megabytes", "MetricName":
              "FreeDisk"}}, {"FullName": "AWS.EC2.Windows.CloudWatch.CloudWatch.CloudWatchOutputComponent,AWS.EC2.Windows.CloudWatch",
              "Id": "CloudWatchMetrics", "Parameters": {"SecretKey": "", "AccessKey":
              "", "NameSpace": "Windows", "Region": "ap-southeast-2"}}, {"FullName": "AWS.EC2.Windows.CloudWatch.CustomLog.CustomLogInputComponent,AWS.EC2.Windows.CloudWatch",
              "Id": "SSMLog", "Parameters": {"Encoding": "UTF-8", "CultureName": "en-US",
              "TimestampFormat": "yyyy-MM-dd HH:mm:ss", "TimeZoneKind": "UTC", "Filter":
              "amazon-ssm-agent.log", "LineCount": "3", "LogDirectoryPath": "C:\\ProgramData\\Amazon\\SSM\\Logs"}},
              {"FullName": "AWS.EC2.Windows.CloudWatch.CloudWatchLogsOutput,AWS.EC2.Windows.CloudWatch",
              "Id": "CloudWatchLogs", "Parameters": {"SecretKey": "", "AccessKey": "",
              "LogStream": "{instance_id}", "Region": "ap-southeast-2", "LogGroup": "/ssm7/SSMLogs"}}]}}'
            status:
            - Enabled
          ScheduleExpression: cron(0 */30 * * * ? *)
          Targets:
          - Key: tag:SSM_ASSOCIATION
            Values:
            - ssm7
        Type: AWS::SSM::Association



Pellentesque elementum eu elit dictum feugiat. Phasellus faucibus, mauris non aliquet vehicula, tortor magna congue neque, elementum laoreet dui quam sed est. Mauris et enim sit amet velit imperdiet tincidunt. Etiam viverra sapien sit amet lacus varius, a euismod odio rhoncus. Morbi pretium fermentum nulla in tempor. Suspendisse luctus orci quis ligula luctus, quis vestibulum ipsum vestibulum. Fusce ut blandit tellus. In sodales justo vel tellus venenatis, ultrices gravida neque fringilla. Duis bibendum nec lectus vel aliquam. Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Sed in varius turpis. Etiam elementum, leo sit amet faucibus iaculis, eros eros sodales nisi, eu euismod justo diam vitae orci. Vivamus vitae turpis at ipsum bibendum fringilla vel sit amet nunc.

Morbi ut semper eros. Curabitur venenatis tellus tortor. Suspendisse convallis quam dapibus gravida tempus. Cras non libero sit amet odio auctor sollicitudin id in orci. Pellentesque facilisis ligula sit amet erat ultricies, sit amet feugiat augue iaculis. Fusce non lectus sit amet nisi iaculis ultrices. Ut pretium dignissim aliquam. Vestibulum id semper est, vitae lobortis erat. Quisque commodo bibendum metus, nec pulvinar eros vulputate ut. Orci varius natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Donec dui sapien, efficitur eget urna id, sollicitudin consequat sem. Vivamus consequat quam sed erat congue malesuada.

Sed ligula ex, lobortis vitae aliquet et, rhoncus et velit. Quisque eu sem eget massa tempor eleifend a quis sem. Maecenas non metus et augue porta auctor sed sit amet urna. Ut a placerat metus. In eu fringilla risus, non porta felis. Aliquam non enim iaculis velit sodales pharetra. Pellentesque gravida arcu vel gravida efficitur. Nunc nec metus nec lectus interdum fermentum.

Phasellus rutrum vestibulum malesuada. Nullam eu risus ultricies, dapibus felis a, pellentesque sapien. Donec sit amet eleifend orci. Morbi imperdiet imperdiet nunc, sed viverra mi congue a. Orci varius natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Donec dignissim sem orci, at iaculis magna egestas in. Fusce hendrerit libero sit amet arcu elementum, at pulvinar nisl posuere.
