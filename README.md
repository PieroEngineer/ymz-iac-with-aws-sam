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
sam deploy --guided --region us-east-1
