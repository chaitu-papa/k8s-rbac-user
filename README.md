# k8s-rbac-user:
Instructions :
1. aws iam create-user --user-name rbac-user
2. aws iam create-access-key --user-name rbac-user | tee /tmp/create_output.json
3.
cat << EoF > rbacuser_creds.sh
export AWS_SECRET_ACCESS_KEY=$(jq -r .AccessKey.SecretAccessKey /tmp/create_output.json)
export AWS_ACCESS_KEY_ID=$(jq -r .AccessKey.AccessKeyId /tmp/create_output.json)
EoF

4.Create IAM Role and IAM Policy

5. 
a. execute ->. rbacuser_creds.sh
b. execute -> aws sts get-caller-identity
c. unset AWS_SECRET_ACCESS_KEY
d. unset AWS_ACCESS_KEY_ID

6.Add user and group aws-auth file (kubectl edit configmap aws-auth -n kube-system)
##Sample
apiVersion: v1
data:
  mapRoles: |
    - rolearn: arn:aws:iam::<accountid>:role/<Nodegrp-role>
      username: system:node:{{EC2PrivateDNSName}}
      groups:
       - system:bootstrappers
       - system:nodes
    - rolearn: arn:aws:iam::<accountid>:role/k8s-developer
      username: demo-app1-user
  mapUsers: |
    - userarn: arn:aws:iam::<accountid>:user/demo-app1-user
      username: demo-app1-user
      groups:
        - k8s-developer
  7. apply roles and role-binding
  8. Test ngnix.yaml (kubectl create -f ngnix.yml -n demo-app1) 
