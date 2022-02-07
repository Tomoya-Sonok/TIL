# CloudFormation における template.yaml

Lambda や API Gateway の設定を書き込む。

```yaml
AWSTemplateFormatVersion: '2010-09-09'

# AWS SAM構文をCloudFormation用に変換する宣言
Transform: 'AWS::Serverless-2016-10-31'

# スタックの説明
Description: An AWS Serverless Specification template describing your function.
Resources:
  TestProgram:
    Type: AWS::Serverless::Function
    Properties:
      FunctionName: Sonok-DevPT-TestProgram
        Handler: index.handler
        Runtime: nodejs14.x
        Layers:
          - !Ref SonokDevPTLayer1ForTestProgram
          - !Ref SonokDevPTLayer2ForTestProgram
        CodeUri: Tomoya-Sonok/TestProgram
        DeploymentPreference:
          Type: AllAtOnce
        Description: 'API for some programs'
        MemorySize: 256
        Timeout: 60
        Environment:
          Variables:
            TEST_TBL: 'Sonok-DevPT-Test_TBL'
            LOG_LEVEL: 'DEBUG'
            SOME_URL: 'http://something.com'
        Role: 'arn:aws:iam::3512270356:role/Sonok-Test-Role'
        VpcConfig:
          SecurityGroupIds:
            - sg-fg0wj04h02h024gr
          SubnetIds:
            - subnet-d6983hf39pgpw3
            - subnet-u1h29f8h24982g
            - subnet-u980gh0aw8h3gp
        Tags:
          Name: TestTag
          Environment: dev
```

詳しくは、[この記事](https://www.wakuwakubank.com/posts/640-aws-sam/)を参照。

# CloudWatch Logs

## 監視設定のやり方

監視したい CloudWatch のロググループごとに設定することができる。

```
手順：

1. メトリクスフィルターの作成
2. アラーム定義の作成 or 既存アラーム定義との紐付け
3. 通知したい SNS トピックを設定
4. アラームが作成済みであることを確認
```

# Step Functions

Lambda 関数の実行時間 15 分制限やレスポンスデータ 1mb 制限を突破して、より複雑で重たい処理を実装したい時に使う。

以下、ステートマシンの定義例。（States の種類や各項目の使用用途等については、[公式ドキュメント](https://docs.aws.amazon.com/ja_jp/step-functions/latest/dg/concepts-states.html)を参照）

```json
"Comment": "My First State Machine",
"StartAt": "ConfigureCount",
"States": {
  "ConfigureCount": {
    "Type": "Pass",
    "Result": {
      "count": 10,
      "index": 0,
      "step": 1
    },
    "ResultPath": "$.iterator",
    "Next": "Iterator"
  },
  "Iterator": {
    "Type": "Task",
    "Resource": "arn:aws:lambda:ap-northeast-1:3512270810308:function:MyLambdaFunction",
    "ResultPath": "$.iterator",
    "Next": "IsCountReached"
  },
  "IsCountReached": {
    "Type": "Choice",
    "Choices": [
      {
        "Variable": "$.iterator.continue",
        "BooleanEquals": true,
        "Next": "LoopProcess"
      },
      {
        "Variable": "$.error.exists",
        "BooleanEquals": true,
        "Next": "OutputException"
      }
    ],
    "Default": "Done"
  },
  "LoopProcess": {
    "Comment": "My application logic, to run a specific number of times",
    "Type": "Pass",
    "Result": {
      "success": true
    },
    "ResultPath": "$.result",
    "Next": "Iterator"
  },
  "Done": {
    "Type": "Pass",
    "End": true
  }
}
```
