# satellite

An #Open-Source automation tool to generate a report of AWS resources created in last X days that can be filtered based on tags. The scan report can be sent as a slack notification or via mail.

This project is currently under Planning / Devlopment phase. You can checkout the proposal and if it interests you by any mean, give the repository a `Star`, feel free to raise an Issue, or DM me on [twitter](https://twitter.com/ameydev2) if you would like to be a collaborator.

# Project (Launch) Proposal

If you own any AWS account (an idividual one or shared across different teams in an your organization) then you probably would have ended up creating some AWS resources in different regions which you lose to keep a track of. These resources are created for a POC or for learnig porposes, and get missed from getting destroyed by you. `So to avoid you from paying unncessary bill for such resources`, you can use the `satellite`.

The `satellite` tool is supposed to scan your AWS account to look out for recently created resources and send an alert mail or a slack notification to the owners.

## How the scanning should work?

If you have used Kubernetes, then you would probably know a concept of an Admission Controller which can validate any incoming request to the cluster. The incoming requests can be of type `CREATE`, `GET`, `UPDATE`, or `DELETE` that can intercepted even before getting created.

Just like the Kunernetes cluster, the AWS resources can be created from different ways (from console, using AWS cli, using IaaC tools like `Terraform` or `CloudFormation`, client SDK libraries like `boto3`, etc.). But unfortunately till this date there no way of setting up a gateway for intercepting these `CREATE`, `GET`, `UPDATE`, or `DELETE` requests made on the resources.

However, there is one way of finding out which resources were created by checking the events from `CloudTrail`. And this is exactly the `satellite` will be leveraging. The `CloudTrail` logs can be stored in an S3 bucket in the form of JSON files. Where it all the metadata of every event that takes place in the account.

All you will need to do is

1. Enable the `CloudTrail` and setup an `S3` bucket as the backup location for the logs.
2. Configure the S3 bucket name in to the `satellite`'s `config.yaml` file.
3. Give the `ReadOnly` permissions of all the resources that you want to scan(make sure the resources are present in the supported list of resources.) to the `satellite` tool.

## Which resources it will Scan?

For the beta release, I am focusing on only those resource which `most widely used` by the community and are `billed`. You can checkout the list of the resources that are in plan.

- EC2 instances
- EKS clusters
- LoadBalancers
- S3 buckets
- DB instances
- DynamoDB tables

The list of the above mentioned resources is likely to increase in the future.

### Filtered scan using Tags

The `satellite` scanning can be skipped for some resources with custom tags. The tag list can be configured in a config.yaml file.
Example skip-tags: `environment=production`, `something=import`, etc.

Basically the `satellite` will generate a scan report of all the resources and those resources which will be having such tags will be ignored in the scan report.

## How to deploy the `satellite`?

The recommened way of deploying the `satellite` would be to deploy it as a Lambda function.

### Why Lambda function?

- A lambda function is a serverless platform which suits perfect for some tool which need not be executed as a Service.
- The `satellite` is supposed be deployed as a Stateless service, it will be storing the state of the resources that were scanned earlier in a remote managed service (probably a RDS db or as a JSON file in an S3 bucket just like Terraform.)
- The billing cost of `satellite` execution should be kept as minimum as possible.

### Possible Deployment/Execution alternatives

- Execute locally as a binary
- Execute it on a already running Kubernetes cluster as a CronJob.

## Future Scope

- Add support for GCP and Azure if possible. :)
- to be continued... :D

## Contributions and Support

Adding contributions, finding bugs or suggesting new features would definitely motivate the creator(s).
