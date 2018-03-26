# AWS-Application-SSL-ForFree
This is a Lambda Function to do the whole hard work when you have multiple websites that uses the same server/load balancing.

I had the idea to create this Lambda Function because the company I've been working for have a Websites Platform to host customer video on demand content.
This platform is 100% based on AWS and use ElasticBeanstalk Application for server side, with all the elasticity, reliabilty and cost optimization as possible. 
The only pilar of the AWS Well Architected Framework that I've missed was probably the most important one, Security.
We've planned to have a few websites in this platform, but as long as our comercial area is pretty awesome, we're currently taking care of more than 1230 websites.
So there's one guy that is working everyday to do the step-by-step that I've optimized here in this Lambda. :)

#Read it first
- This function is made using Node.JS version 6.10. I'll use a lot the AWS SDK for Javascript.

- The next steps are describing the ways that functions work by exploring each one. Look at the index.js file to see how it's really working.

#1. This function will be called from inside of the AWS Console or using API Gateway pointing to Lambda function. 
(I'll not cover how to do it at this moment.) For this example, I'll just need the domain (www.example.com).


- I'm assuming you're a developer with Javascript knowledge and pretty awesome with AWS Services like Route53, Elastic Load Balancing,  Cloudfront, Certificate Manager and other AWSome products.
#2. Validate your Route53 Hostedzone Exists.
If you do not have registered your Route53 hostedzone, it will do nothing. 
So, validate it and get the hostedzone information to use along the function call.

#3. Request the Certificate using Amazon Certificate Manager 
Remember to add the Subdomain (*.example.com).
Also remember to use DNS Validation. As long as we're the owner of the domain and it's in Route53, we'll be able to do DNS Validation instead of E-mail validation. (It's scriptable)

#4. Add the tag to Certificate.
It's just a formality but as long as you're using some tecnology to take care about when it's going to expire, or something like it, it should be a good option. Remember to put at least the Name Tag.

#5. Describe the Certificate you requested.
This is something that I'd like AWS change in the AWS SDK. When you request the certificate, it doesn't send in the callback the DNS Validation properties, which makes you do another request.

#6. Register CMAME Records on Route53
When you've selected DNS Validation and made a call to DescribeCertificate, it will show you all you need to create the CNAME records in your Hostedzone.
So, do it and use the information you got when in the second step.

#7. Create a new Distribution on Cloudfront
This is probably the hardest step. When you create a distribution, you're creating an Origin and a Behavior too. So this is really a mess. Remember to put all the requests to be redirected to https, choose the HTTP Methods you want to allow (all in my case) and so forth.

- I really think you should take a real look at this moment, because there's a lot of possibilities in one Cloudfront Distribution. If you have any doubt, ask me, right?

#8. Change/Create new Route53 A and CNAME Record to point to Cloudfront instead of Load Balancing.
In my case, I was using this domain without SSL, so it was "looking" to the Load Balancing directly. In this step, all you need to do is update it to look to Cloudfront distribution you've just created.
So the recordsets should be like: 
example.com - A - d12837192379127.cloudfront.net (fake address)
*.example.com - CNAME - example.com (just some idea)

(I didn't finish this last method yet.)



