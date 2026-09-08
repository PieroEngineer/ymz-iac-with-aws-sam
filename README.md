# 1. Validate template syntax
sam validate --lint

# 2. Buid the lambda source
sam build

# 3. Check the result
ls -la .aws-sam/build/HonoApiFunction/

# 4. Deploy all nested stacks in order  ("--guided" for the first trigger to build the environment variables in "samconfig.toml")
sam deploy --guided --region us-east-1
