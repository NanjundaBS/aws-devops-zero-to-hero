1.  **Restricting Access to an S3 Bucket using Bucket Policies**
2.  **Hosting a Static Website on S3**

Let's break down each demo:

**Demo 1: Restricting Access to an S3 Bucket using Bucket Policies**

This demo illustrates how to restrict access to a specific S3 bucket, even for users who have broad S3 permissions via Identity and Access Management (IAM). The goal is to make a bucket containing sensitive information accessible only to specific users or administrators, overriding general IM policies.

Here are the steps performed:

1.  **Create an S3 Bucket and Upload an Object**: A bucket named `app1-payments-prod-example.com` is created in a chosen AWS region. An object (an `index.html` file) is uploaded to this bucket. This object is described as being sensitive information, like a database dump.

2.  **Create an IAM User**: A new IAM user named `demo S3 buckets user` is created. Initially, this user is given access to the AWS Management Console with a custom password but **no specific AWS permissions are attached**.

3.  **Log in as the IAM User and Verify Initial Access**: The presenter logs into the AWS Management Console using the newly created `demo S3 buckets user` in a separate private/incognito browser window. When trying to access the S3 service and list buckets, the user receives an "Insufficient permissions" error, which is the expected behavior as no S3 permissions were granted yet.

4.  **Grant the IAM User Broad S3 Permissions**: Back in the main AWS console window (logged in as an administrator or root user), the presenter adds permissions to the `demo S3 buckets user` by attaching the `AmazonS3FullAccess` managed policy. This policy grants full access to all S3 buckets.

5.  **Verify Full Access Before Restriction**: The presenter refreshes the S3 console in the incognito window (logged in as the `demo S3 buckets user`). Now, the user **can see all S3 buckets, including the target sensitive bucket (`app1-payments-prod-example.com`)**. The user can click on the bucket and **successfully download the object (`index.html`)**, demonstrating that `AmazonS3FullAccess` allows access to everything by default.

6.  **Configure Bucket Permissions (Bucket Policy)**: To restrict access to the sensitive bucket, the presenter navigates back to the bucket's properties in the main AWS console and finds the "Bucket permissions" section. The "Bucket policy" needs to be edited.

7.  **Write the Bucket Policy**: The presenter uses the policy editor/generator to create a JSON policy.
    *   A statement is added with the **Effect set to `Deny`**.
    *   The **Principal is set to `*`**, meaning this denial applies to everyone in AWS.
    *   The **Action is set to `s3:*`**, denying all S3 actions.
    *   The **Resource is set to the ARN of the specific S3 bucket and all objects within it** (e.g., `arn:aws:s3:::app1-payments-prod-example.com` and `arn:aws:s3:::app1-payments-prod-example.com/*`). (A minor correction to the resource ARN format was noted by the presenter, available in the GitHub document).
    *   A **Condition is added to make an exception** for the administrator/bucket owner. The condition uses `StringNotEquals` on `aws:PrincipalArn` to ensure the `Deny` effect does *not* apply to the specified administrator IAM user's ARN. This allows the administrator to still access the bucket.

8.  **Save the Bucket Policy**: The crafted JSON policy is saved for the bucket.

9.  **Test Access After Restriction**: The presenter switches back to the incognito window (logged in as the `demo S3 buckets user` with `AmazonS3FullAccess`). Upon trying to access or list objects within the `app1-payments-prod-example.com` bucket, the user now receives an "Insufficient permissions to list objects" or "Access denied" error. This confirms that the **bucket policy successfully restricted access** for this specific bucket, overriding the broader IAM policy.

This concludes the first demo, showing how bucket policies can be used to control access at the bucket level, independent of or in addition to IAM user/group policies.



**Demo 2: Hosting a Static Website on S3**

This demo demonstrates how to use S3 to host a static website, highlighting its cost-effectiveness and global accessibility for this purpose.

Here are the steps performed:

1.  **Prepare the Website Content**: The presenter uses a simple `index.html` file. This is the main page of the static website.

2.  **Create an S3 Bucket and Upload Content**: A bucket is created (or the one from the previous demo is used). The `index.html` file is uploaded into this bucket.

3.  **Enable Static Website Hosting**: Navigate to the bucket's properties and scroll down to the "Static website hosting" section. Click "Edit" and **select "Enable" static website hosting**.

4.  **Configure Index Document**: Specify the **"Index document"** as `index.html`. An error document can also be configured but is skipped in this demo.

5.  **Save Static Website Hosting Configuration**: Save the changes to the bucket properties.

6.  **Note the Website Endpoint URL**: After saving, AWS provides a "Bucket website endpoint" URL. This is the URL to access the hosted static website.

7.  **Attempt to Access Website (Initial - Forbidden)**: The presenter clicks on the provided website endpoint URL. The browser displays a "Forbidden" error. This is because **public access to the bucket is blocked by default**.

8.  **Disable Block All Public Access**: To allow public access for a website, navigate back to the bucket's "Permissions". Find the "Block public access (bucket settings)" section and **uncheck the box that says "Block all public access"**. Confirm the change. (Note: organizational settings can potentially prevent this step).

9.  **Attempt to Access Website (Second Attempt - Access Denied)**: The presenter attempts to access the website URL again after disabling "Block all public access". It might still show "Access Denied". This happens because while public access is no longer blocked *at the settings level*, there is **no specific permission granted** via a bucket policy to *allow* public users to read the objects (`s3:GetObject`).

10. **Add Bucket Policy to Grant Public Read Access**: Go back to the bucket's "Permissions" and add a new statement to the "Bucket policy".
    *   The **Effect is set to `Allow`**.
    *   The **Principal is set to `*`**, meaning this applies to everyone (the internet).
    *   The **Action is set specifically to `s3:GetObject`**, granting permission only to retrieve objects.
    *   The **Resource is set to the ARN of the S3 bucket *objects*** (e.g., `arn:aws:s3:::app1-payments-prod-example.com/*`).

11. **Save the Bucket Policy**: Save the updated bucket policy.

12. **Access Website (Success)**: The presenter attempts to access the website URL for the final time. The **static website (`index.html`) loads successfully** in the browser, demonstrating that S3 is now hosting the website and allowing public read access to the index document via the specific bucket policy.

This completes the second demo, showing the necessary steps and permissions required to host a static website on S3. The presenter also briefly mentions that for websites with JavaScript making API calls to other sites, Cross-Origin Resource Sharing (CORS) might need to be enabled.
