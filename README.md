# 1. Validate template syntax
sam validate --lint

# 2. Buid the lambda source
sam build

# 3. Check the result
- PowerShell: 
    ```Get-ChildItem -Force .aws-sam/build/LambdaModule/YummiziEngineFunction```
- Git Bash / WSL: 
    ```ls -la .aws-sam/build/LambdaModule/YummiziEngineFunction/```

# 4. Deploy all nested stacks in order
## ("--guided" for the first trigger to build the environment variables in "samconfig.toml")
sam deploy --guided --region us-east-1 --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM CAPABILITY_AUTO_EXPAND

## Then
sam deploy


# For reattemps (Run this command in your terminal to delete the locked stack:)
aws cloudformation delete-stack --stack-name yummizi-backend --region us-east-1
aws cloudformation wait stack-delete-complete --stack-name yummizi-backend --region us-east-1



# The Speed Shortcut (SAM Sync) 🚀 
* First research about the impact of the enlarged ".aws-sam" 
If you are actively developing and don't want to wait for CloudFormation to process a deployment stack, AWS SAM has a built-in feature called sam sync designed exactly for code updates. It bypasses CloudFormation entirely and updates your Lambda function directly in about 5 seconds.

Run this command in your terminal:

```sam sync --code --stack-name yummizi-backend --watch```

- What it does:
The --code flag tells SAM to ignore infrastructure changes and only update code artifacts. The --watch flag stays active in your terminal—every time you hit "Save" on your Hono files, SAM will instantly rebuild and re-upload the changes in the background automatically. (You can just remove "--watch" for one-shoot update)


# To clean the data
## Cognito  (it should be the policy "DeletionPolicy: Delete")
aws cloudformation delete-stack --stack-name yummizi-backend

# To test the CORS
curl -i -X OPTIONS "https://<api-id>.execute-api.us-east-1.amazonaws.com/dev/restaurantProfile" -H "Origin: http://localhost:5173" -H "Access-Control-Request-Method: GET" -H "Access-Control-Request-Headers: authorization,content-type"


curl -i https://xwehhxf9wa.execute-api.us-east-1.amazonaws.com/dev/restaurantProfile -H "Authorization: Bearer 123asd"




1. /dist/aws/clientFactory.js is written in CJS, but is interpreted as ESM. Consider using the .cjs extension, e.g. /dist/aws/clientFactory.cjs

2. /dist/routes/requests.js is written in CJS, but is interpreted as ESM. Consider using the .cjs extension, e.g. /dist/routes/requests.cjs

3. /dist/routes/restaurants.js is written in CJS, but is interpreted as ESM. Consider using the .cjs extension, e.g. /dist/routes/restaurants.cjs

4. /dist/scripts/seed.js is written in CJS, but is interpreted as ESM. Consider using the .cjs extension, e.g. /dist/scripts/seed.cjs