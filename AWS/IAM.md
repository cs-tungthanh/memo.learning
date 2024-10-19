# Organization
- Global service

- Master Account
- Child Account
1. Create an organization on Master Account


## IAM Roles

- when you assume a role (user, app or service) you **give up your original permission** and take the permission assigned to the role

## IAM Permission Boundary (for IAM Entity: user, role)
- using to set the maximum permission that an identity-based policy can grant to an entity.
- Example: an account have been set an permission boundary for FullAccessS3, then if we attach a new policy (IAM FullAccess) => the result permission is nothing.
- 