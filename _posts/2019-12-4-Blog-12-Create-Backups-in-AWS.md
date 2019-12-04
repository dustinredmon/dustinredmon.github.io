---
layout: post
title: Blog 12 - Create a Backup in AWS
---

This week for my Senior Design Project, I am creating a backup plan for my AWS infrustucture. There are a couple of ways to create a backup in AWS. You can use a LAMDA function to scheulde snapshots of your EBS volumes, or you can create custom AMIs. But he most conienient option by far is to use AWS Backup. Using AWS Backup, you can centrally configure backup policies and monitor backup activity for AWS resources, such as Amazon EBS volumes, Amazon RDS databases, Amazon DynamoDB tables, Amazon EFS file systems, and AWS Storage Gateway volumes. AWS Backup automates and consolidates backup tasks previously performed service-by-service, removing the need to create custom scripts and manual processes. With just a few clicks in the AWS Backup console, you can create backup policies that automate backup schedules and retention management. 

The first step is to create a backup plan. On the AWS Backup console, choose Create backup plan. There are two ways you can create a Backup Plan. You can start from an existing Backup Plan, or create a new one from scratch. I would recommend starting from an existing backup plan and editing the configuration to suit your needs. In the Resource assignment name list, choose a name for the resource assignment. Here you want to specify the volumes you want to backup. When you create an EC2 instance, a volume is automatically created to store data and files for that instance. This is what is actually being backep up. You can see your volumes in EC2 console if you want to browse the volumes that have been created. Under IAM role, choose Default role. In the Assign resources section, select resources to back up either by tags or volume ID.

The next thing you need is a Backup Vault. A backup vault is where your backups are stored. You can use the default Backup Vault or create a new one. To create a backup vault, on the AWS Backup console, in the navigation pane, choose Backup vaults. Choose Create backup vault. Enter a name for your backup vault. Select an AWS KMS key. You can use either a key that you already created, or the default AWS Backup master key. For my project I used the default backup key. When you are finished, choose Create backup vault.

The last thing you need to do is create backup rules. A lot of things can be configured in the back up rules, but the most important is selecting the frequency. This will schedule your backups for when you want your backups to run. You can schedule your backup to execute whenever you like; Weekly, bi-weekly, monthly, daily, yearly. Each backup rule also consists of the following elements: Backup Rule Name Backup Frequency, Backup Window, Lifecycle, Backup Vault.

Here is how implemented my Backup implementation using terraform:

    resource "aws_backup_vault" "np-vault" {
      name        = "NPVault"
    }

    data "aws_ebs_volume" "bastion-volume" {
      most_recent = true

      filter {
        name   = "attachment.instance-id"
        values = ["${aws_instance.bastion.id}"]
      }
    }

    data "aws_ebs_volume" "server1-volume" {
      most_recent = true

      filter {
        name   = "attachment.instance-id"
        values = ["${aws_instance.server-1.id}"]
      }
    }

    data "aws_ebs_volume" "server2-volume" {
      most_recent = true

      filter {
        name   = "attachment.instance-id"
        values = ["${aws_instance.server-2.id}"]
      }
    }

    resource "aws_backup_plan" "np-backup" {
      name = "NPBackupPlan"

      rule {
        rule_name         = "Weekly"
        target_vault_name = "${aws_backup_vault.np-vault.name}"
        schedule          = "cron(0 1 ? * 2 *)"
      }
    }

    data "aws_iam_role" "backup-role" {
      name = "AWSBackupDefaultServiceRole"
    }

    resource "aws_backup_selection" "np-resources" {
      iam_role_arn = "${data.aws_iam_role.backup-role.arn}"
      name         = "NPWeatherApp"
      plan_id      = "${aws_backup_plan.np-backup.id}"

      resources = [
        "${data.aws_ebs_volume.bastion-volume.arn}",
        "${data.aws_ebs_volume.server1-volume.arn}",
        "${data.aws_ebs_volume.server2-volume.arn}",
        "${aws_dynamodb_table.dynamodb-tf-state-lock.arn}",
      ]
    }
    
The important thing to note here if you want to use terrafom instead of the amazon console, you need to create data sources for all of your EBS volumes so that you can pull theier ARN to be referenced. I did not have to create a data source for my dynamoDB table because I created the table in my Terraform file, so I already know the name of the table. 
