# aws-transcribe
_Early version of the AWS architecture for the Pre-Transcribe tool._
<img src="/arch-transcribe.png"/>

Here is the yaml code which I will use in cloudformation:

This CloudFormation template creates the S3 bucket with subdirectories, Lambda functions, and IAM roles for those Lambda functions. There are placeholders in this code which need the proper updates. Also, zip files for the Lambdas.

''' yaml
AWSTemplateFormatVersion: "2010-09-09"
Description: Create S3 Bucket with Subdirectories and Lambda Functions

Resources:
  MediaBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: my-media-bucket
      AccessControl: Private

  RawMediaSubdirectory:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket: !Ref MediaBucket
      PolicyDocument:
        Statement:
          - Action: s3:*
            Effect: Allow
            Resource: 
              - !Sub ${MediaBucket.Arn}/rawmedia/*
            Principal: "*"

  Mp3ConsumeSubdirectory:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket: !Ref MediaBucket
      PolicyDocument:
        Statement:
          - Action: s3:*
            Effect: Allow
            Resource: 
              - !Sub ${MediaBucket.Arn}/mp3consume/*
            Principal: "*"

  TranscoderSubdirectory:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket: !Ref MediaBucket
      PolicyDocument:
        Statement:
          - Action: s3:*
            Effect: Allow
            Resource: 
              - !Sub ${MediaBucket.Arn}/transcoder/*
            Principal: "*"

  ResultsSubdirectory:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket: !Ref MediaBucket
      PolicyDocument:
        Statement:
          - Action: s3:*
            Effect: Allow
            Resource: 
              - !Sub ${MediaBucket.Arn}/results/*
            Principal: "*"

  RawMediaLambdaRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: RawMediaLambdaRole
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service: lambda.amazonaws.com
            Action: sts:AssumeRole
      Policies:
        - PolicyName: S3AccessPolicy
          PolicyDocument:
            Version: "2012-10-17"
            Statement:
              - Effect: Allow
                Action:
                  - s3:GetObject
                  - s3:ListBucket
                Resource: !Sub ${MediaBucket.Arn}/rawmedia/*

  Mp3ConsumeLambdaRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: Mp3ConsumeLambdaRole
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service: lambda.amazonaws.com
            Action: sts:AssumeRole
      Policies:
        - PolicyName: S3AccessPolicy
          PolicyDocument:
            Version: "2012-10-17"
            Statement:
              - Effect: Allow
                Action:
                  - s3:GetObject
                  - s3:ListBucket
                Resource: !Sub ${MediaBucket.Arn}/mp3consume/*

  TranscoderLambdaRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: TranscoderLambdaRole
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service: lambda.amazonaws.com
            Action: sts:AssumeRole
      Policies:
        - PolicyName: S3AccessPolicy
          PolicyDocument:
            Version: "2012-10-17"
            Statement:
              - Effect: Allow
                Action:
                  - s3:GetObject
                  - s3:ListBucket
                Resource: !Sub ${MediaBucket.Arn}/transcoder/*

  ResultsLambdaRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: ResultsLambdaRole
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service: lambda.amazonaws.com
            Action: sts:AssumeRole
      Policies:
        - PolicyName: S3AccessPolicy
          PolicyDocument:
            Version: "2012-10-17"
            Statement:
              - Effect: Allow
                Action:
                  - s3:GetObject
                  - s3:ListBucket
                  - s3:PutObject
                Resource: !Sub ${MediaBucket.Arn}/results/*

'''
