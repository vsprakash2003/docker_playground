## to create the client application
```
cd docker_playground
mkdir client
npx create-react-app client
```
** if you get an error message that "templates cannot be created" and you notice that src folder is not created, it means that there is a global create-react-app. Delete the global create-react-app **

** use `yarn global remove create-react-app` to delete the global create-react-app **

## setup travis site for CI/CD status
```
1. sign into https://travis-ci.org with github credentials
2. projects from github are automatically pulled and available
3. select the project for which you need to setup CI/CD
4. click on more options on the right and select settings
5. scroll down to Environment variables section and add 2 new environment variables
6. Name these variables as DOCKER_ID and DOCKER_PASSWORD
7. ** please note that symbols in passwords need to be preceded with an escape character (\) **
```

## to commit to master branch
```git commands
git init
git add .
git commit -m "first commit"
git remote add origin https://github.com/vsprakash2003/docker_playground
git push -u origin master
```

### verify docker containers built by travis are available in dockerhub
```
1. login to dockerhub - https://hub.docker.com/
2. verify that the newly published repositories are available
3. if they are not visible on the landing page, click on Repositories menu and see if they are available
```

## setup Amazon Elastic Beanstalk (EBS)
```
1. login to https://amazon.com
2. search for EBS (Elastic Bean Stalk) in the AWS services search
3. click on create new application button
4. specify application name ex: multi-docker
5. the page is now displayed with message "No environments currently exist for this application. Create one now"
6. click on create one now link
7. select the web environment box
8. select Multi-Docker conatiner from the pre-configured platform drop down
9. click create new environment
```

## setup postgres database
```
1. search for RDS (Relational DB Service) in the AWS services search
2. scroll down to the create database section
3. click the create database button
4. select postgres for the engine options
5. select the RDS free tier in the templates section
6. provide a name for the DB instance identifier in the settings section. ex: multi-docker-postgres
7. enter master password
8.ensure auto scaling is unselected
9. select choose new for the VPC security group
10. provide a new security group name. ex: multi-docker
11. expand additional configuration section and under database name provide a new name ex: fibvalues
12. scroll down and click on create database button
```

## setup redis store
```
1. search for Elasticache in the AWS services search
2. click on Redis in the left menu
4. in the redis setup page, provide the name ex: multi-docker-redis
5. in the node type drop down, click the t2 tab and select cache.t2.micro
6. update the value for number of replicas to 0
7. provide a name for the subnet group ex: redis-group
8. select all the subnets listed
9. press the create button
```

## create a new security group
```
1. search for VPC in the AWS services search
2. click on security groups from the security section in the left menu 
3. click on the create button
4. in the security group landing page, edit the name of the newly created security group to match the group name
5. on the tabs below, select inbound rules and click edit
6. click the add new rule button
7. provide the port range as 5432-6379 for the default custom TCP rule
8. on the source drop down, select custom 
9. in the next input box, type the id of the security group that was just created. you can find the id just above the inbound rules tab
10. click on save rules
```

## apply the security group to Postgres, Redis and EBS
```
1. search for Elasticache in the AWS services search
2. click on Redis in the left menu
3. select the Redis instance that you had created
4. in the actions drop down button, select modify
5. click the edit option in the VPC security groups
6. click the newly created security group. Also leave the default security group selected
7. click the modify button
8. search for RDS in the AWS services search
9. click databases on the left menu
10. select the postgres instance that was created sometime back
11. click on modify button
12. in the security group dropdown select the security group that was newly created
13. click the continue button
14. in the next screen, scroll down to the bottom and select apply immediately and click modify DB instance
15. click on services menu
16. locate Elastic Beanstalk on the history in the left
17. click on the EBS instance that was created
18. click on configuration from the left menu
19. click the modify button against the instances
20. scroll down and in EC2 security groups, select the newly created security group and click apply
21. click on confirm button in the next screen
```

## update the environment variables in EBS
```
1. click on services menu
2. locate Elastic Beanstalk on the history in the left
3. click on the EBS instance that was created
4. click on configuration from the left menu
5. click the modify button against software
6. enter the first environment variable REDIS_HOST
7. click on the services at the top, select Redis and open in a new tab
8. click the expand arrow next to the redis instance that was created
9. copy the primary end point. leave the port out of the copy
10. paste the value against the REDIS_HOST environment variable
11. for the next environment variable REDIS_PORT, set value as 6379
12. Here are the other environment variables that need to be entered
    PGUSER - postgres
    PGPASSWORD - the password you entered while creating the RDS instance
    PGSUER - click services, select RDS, click on databases, select the postgres instance, copy the end point
    PGDATABASE - fibvalues
    PGPORT - 5432
13. click apply
```

## setup IAM user and access policies
```
1. search for IAM in the AWS services search
2. click on users in the left
3. click on add user
4. provide a name for the user ex: multi-docker-deployer
5. check the programmatic access for the AWS access type
6. click on next permissions button
7. click on attach existing policies tab at the top
8. in the filter policies, search for beanstalk and check all the policies that are pulled up
9. click on next:tags button
10. click on next review button
11. click on create user button
12. copy the access key id and the secret key generated
```

## add the AWS user and secret key to travis for deployment
```
1. sign in to https://travis-ci.org
2. add 2 environment variables AWS_ACCESS_KEY and AWS_SECRET_KEY
3. the value for these keys will be the AWS access key and secret key generated in the previous setp
```

## specify bucket name and bucket path in the deploy services of travis.yml
```
1. to get the bucket name, search for S3 in the AWS services search
2. click the bucket that has been created
3. bucket name is the name of the bucket created
4. bucket path is any name that can be given
```
