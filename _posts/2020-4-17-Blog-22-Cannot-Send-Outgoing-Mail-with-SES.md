---
layout: post
title: Blog 22 - Cannot Send Outgoing Mail Using Simple Email Service (SES Setup Part.2)
---

A few weeks ago I started working on the AWS Simple Email Service implementation for my senior design project. But for the most part, everything I did was a pseduo implementation rather than an actual implementation. Meaning, I had an idea of how everything should work in theory, but did not actually get around to actually trying anything out mainly because I was waiting on another group member to finish his work before I started mine. So in Blog 17 I discussed how to obtain SES SMTP credentials so that you can send email through Amazon SES by using applications or programming languages that use the SMTP protocol. However there is a little bit more work to be done before you can start sending emails, so this blog will serve as a continuation of Blog 17.

After creating SMTP credentials for SES, I thought I could use those credentilals to login with my app using PHPmailer to send emails, and avoid using the Gmail account that I was using previously as a mail server. However I quickly found out that you still need to provide an email in AWS for SES to be able to send emails from. This should have been obvious to me, but I was a little ignorant to the fact of how everything would just magically work. Of course I still need to register an email account with AWS to send emails from, how else did I think the emails were gonna get sent?

So if you have not picked up on it yet, after you create SMTP credentials as outlined in Blog 17, you will need to register the email you plan to send emails from in AWS, and then you need to verify that email through a confomation link. To do so, open the SES console in AWS. From there, click on "Email Addresses" under "Identitify Management" in the left Navigation Pane. Click the button that says "Verify A New Email Address". Enter the email you would like to verify, and the click "Verify this Email Address". You will now be sent a confirmation link to that email address. Open your email and click on the link that was sent to verify that you are the owner of that address. You should now be able to send a test email. Or should you?

Here is the part where I pratically pulled all the hair out of my head. After I verfied my email address, I tried to send out a test email with PHPmailer. But for some reason, even though my email was verified, and I was using the SMTP credentials I created, I could not get PHPmailer to send an email. I turned on the debug mode to see what was wrong. And I got the following message:

    554 Message rejected: Email address is not verified. The following identities failed the check in region
    
I was completely stumped by this for hours. The email address I was using was clearly verfied, and I kept creating new SMTP credentials trying to get anything to work. Nothing. So after hours of frustrating google searches I found the follwoing information:

    To help prevent fraud and abuse, and to help protect your reputation as a sender, we apply certain restrictions to new Amazon SES accounts.

    We place all new accounts in the Amazon SES sandbox. While your account is in the sandbox, you can use all of the features of Amazon SES. However, when your account is in the sandbox, we apply the following restrictions to your account:

      - You can only send mail to verified email addresses and domains, or to the Amazon SES mailbox simulator.

      - You can only send mail from verified email addresses and domains.

        Note:
        This restriction applies even when your account isn't in the sandbox.

      - You can send a maximum of 200 messages per 24-hour period.

      - You can send a maximum of 1 message per second.
      
This means that the reason why I could not send any emails is because, by default, your AWS account is in "sandbox mode" which restricts you to only sending mail to verified email addresses. This means, in addition to verifying the email you are sending mail from, you also need to verify the email you are sending mail to as well. Which made no sense to me at the time! In a realistic scenerio, how on earth could I get a customer to verify an email if I can't send them email at all. Not to mention verifying a customers email in my AWS console is actually insane. 

But I soon learned that these restrictions are put in place to prevent scammers and spammers from abusing AWS services. To be taken out of "Sandbox Mode" you can submit a ticket to support and explain why you would like the restriction removed, and they will remove it for you. But for testing purposes we can just verify the emails we plan on using as receviers. Since the app is not going live, there isn't any reason to remove the restriction. As long as we can veryify that the app works, that is all that is really important. And it is a relief to figure out why my emails were nto being sent. 

So after verifying the recipients address in the SES console, I was finally able to send a test email, and everything finally worked as expected!
