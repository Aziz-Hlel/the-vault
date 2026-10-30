---
tags:
  - infra/cdn
  - cloud/aws/cloudfront
  - concept
  - best-practices
---



- Apparently there s a cap on number of origins and behaviors in each plan ,
- On pay-as-you-go, the default quota is 25 origins and 25 cache behaviors per distribution, free tier has 5, so far based on deepseek aws official documentation does not list the Pro tier's "maximum origins", even with claude code it gave the 25 value but didnt mention the pro one even though we were talking about it rather he just said to check the documentation 
- when creating the distribution pick `Single website configuration` as a type , The multiple-website option is for serving the same setup under many different domains (like a SaaS giving each customer their own domain). Your approach is the opposite: one domain, with projects separated by path and behavior
- **One catch:** CloudFront passes the full path to the bucket. A request for `/project1/img.jpg` looks for the key `project1/img.jpg` in that bucket. So either put each project’s files inside a `project1/` folder in its own bucket (easiest), or use a CloudFront Function to strip the prefix.

### Create an Origin

- check `Origin access control settings (recommended)` and click on `Create new OAC`, leave the default settings
- then go to the bucket under `Permission` -> `Bucket policy` and add this : 
```json
{
  "Version":"2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCloudFrontServicePrincipalReadOnly",
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::amzn-s3-demo-bucket/*",
      "Condition": {
        "StringEquals": {
          "AWS:SourceArn": "arn:aws:cloudfront::111122223333:distribution/<CloudFront distribution ID>"
        }
      }
    }
  ]
}
```



### Add an S3 as a logging destination for a distribution logs

- It's best to made all distribution save logs into the same s3, less hassle, and you can just add a simple and free (had to point this out cuz yk how aws be) config into the s3 itself to delete any file that passed a certain time like 30 days ago
- under `loggin` click on `Create access log delivery`
- on additional settings:
- ai recommended JSON as output format   
- for partitioning choose something like: `{DistributionId}/`, note that aws will automatically append `AWSLogs/{account-id}/CloudFront/` at the start of your path, now this way you can add other distributions logs to the same s3 instance, each in his own folder
- Pick these fields some references to help you figure out the naming of these fields : `s` stand for server, `c` for client, meaning `c-ip` stands for client Ip

| Field              | Why                                         |
| :----------------- | :------------------------------------------ |
| date, time         | When                                        |
| cs-uri-stem        | Path, to split usage per project            |
| sc-bytes           | Bandwidth per request                       |
| sc-status          | Errors like 403 or 404                      |
| cs(Referer)        | Which site embedded your media (hotlinking) |
| c-ip               | Spotting abusive clients                    |
| cs(User-Agent)     | Spotting bots and scrapers                  |
| x-edge-result-type | Cache hit or miss                           |
| sc-content-type    | Images vs. video                            |
| c-country          | Which country traffic comes from            |

