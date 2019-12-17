# ComposeWebApp

Project notes:

1. This app largely takes the directions from <a href=https://aws.amazon.com/blogs/compute/hosting-asp-net-core-applications-in-amazon-ecs-using-aws-fargate/>Hosting ASP.NET Core applications...</a> and extends them.

This project consists of 3 repositories: ComposeWebApp (the current one), <a href=https://github.com/monish-raj/ReverseProxy>nginx</a> reverse proxy, and <a href=https://github.com/monish-raj/FirstRazorWebApp>the actual .NET app</a>.

Pull each of the above 3 into separate subdirs under the same directory.  Navigate into ComposeWebApp, and build using docker-compose build.

To run locally, use docker-compose run.  Prior to doing that, in nginx.conf, comment the line that says "server 127.0.0.1:5000", and uncomment the previous line which says "server webapp:5000".  

To run on AWS - use as is, keep the line with 127.0.0.1:5000.

The built containers would need to be registerd as ECR repositories first.  Then, you can setup an ECS task and service.

Next, setup a domain name via Route53, security certificate through Cert Manager, then setup an Alias record in Route53 pointing to the ALB. 

In my account, I setup ALB, target group and the Fargate task within a separate non-default VPC, with 2 subnets for ALB redundancy, and SG rules setup to allow communication with the Fargate task.  ALB Listener has been setup to redirect port 80 traffic to 443, and 443 traffic is forwarded to the target group with contains the ECS task - which actually gets registered when the Task/Service is instantiated.  Client SSL traffic is terminated at the ALB, internal communication to the container (nginx) is on port 80.

2. I have a second part of this website setup as a static website in S3.  However, it does not use static hosting.  Instead, the domain is pointed to CloudFront, which has the S3 bucket setup as an origin, and uses an OAI to access the bucket.  Appropriate bucket policy was setup in S3 to allow the origin identity.


