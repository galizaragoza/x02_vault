```sh
# Who am I?
aws sts get-caller-identity

# What policies are attached to my user?
aws iam list-attached-user-policies --user-name <your-user>
aws iam list-user-policies --user-name <your-user>

# Inline policies reveal intent, not just managed ones
aws iam get-user-policy --user-name <your-user> --policy-name <policy-name>

# What roles can I assume?
aws iam list-roles --query 'Roles[?AssumeRolePolicyDocument.Statement[?Principal.AWS]]'
```