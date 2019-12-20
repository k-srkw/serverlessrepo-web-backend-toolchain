# デプロイ方法

1. 以下のコマンドでソースコードを格納する S3 Bucket を展開する stack を作成する。

    ```
    $ aws cloudformation create-stack --stack-name web-backend-bucket --template-body file://web-backend-bucket.yaml --parameters ParameterKey=AppId,ParameterValue=web-backend
    ```

2. 以下のコマンドでソースコードを zip にする

    ```
    $ cd web-backend
    $ zip -r web-backend.zip * .eslintrc.json .gitignore
    ```

3. 以下のコマンドでソースコードを S3 Bucket に格納する。

    ```
    $ REGION=ap-northeast-1
    $ ACCOUNT_ID=XXXXXXXXXXXX
    $ SOURCE_CODE_BUCKET_KEY=web-backend.zip
    $ SOURCE_CODE_BUCKET_NAME=aws-"${REGION}"-"${ACCOUNT_ID}"-web-backend-source
    $ aws s3 mv "${SOURCE_CODE_BUCKET_KEY}" s3://"${SOURCE_CODE_BUCKET_NAME}"/
    ```

4. 以下のコマンドで CodeCommit, CodeBuild, CodePipeline, S3 Bucket (artifact 格納用) を展開する stack を作成する。

    ```
    $ cd ../
    $ APP_RES_ARNS=arn:aws:lambda:"${REGION}":"${ACCOUNT_ID}":function:web-backend-getAllItemsFunction-*\\,arn:aws:lambda:"${REGION}":"${ACCOUNT_ID}":function:web-backend-getByIdFunction-*\\,arn:aws:lambda:"${REGION}":"${ACCOUNT_ID}":function:web-backend-putItemFunction-*\\,arn:aws:dynamodb:"${REGION}":"${ACCOUNT_ID}":table/web-backend-SampleTable-*

    $ aws cloudformation create-stack --stack-name serverlessrepo-web-backend-toolchain --template-body file://serverlessrepo-web-backend-toolchain.yaml --parameters ParameterKey=AppId,ParameterValue=web-backend ParameterKey=RepositoryName,ParameterValue=web-backend ParameterKey=SourceCodeBucketKey,ParameterValue="${SOURCE_CODE_BUCKET_KEY}" ParameterKey=SourceCodeBucketName,ParameterValue="${SOURCE_CODE_BUCKET_NAME}" ParameterKey=AppResourceArns,ParameterValue="${APP_RES_ARNS}" --capabilities CAPABILITY_NAMED_IAM CAPABILITY_AUTO_EXPAND
    ```

5. 上記 stack 作成後、展開が完了すると CodePipeline の CI / CD パイプラインが実行され、SAM (API Gateway, Lambda, Dynamo DB) アプリケーションが展開される

# クリーンアップ方法

1. 以下のコマンドで stack を削除する。

  ```
  $ aws cloudformation delete-stack --stack-name web-backend-bucket
  $ aws cloudformation delete-stack --stack-name web-backend
  $ aws cloudformation delete-stack --stack-name serverlessrepo-web-backend-toolchain
  $ aws cloudformation delete-stack --stack-name serverlessrepo-web-backend-toolchain --retain-resources S3Bucket
  ```

2. 管理コンソールの cloudformation の画面で該当の stack が削除されていることを確認する。
3. その後管理コンソールから該当する S3 Bucket を削除 (バージョン管理を有効にしている場合、CLI からは削除できないため) 。
