<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AWS CloudFront Distribution Setup</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6;
            color: #1f2937;
        }
    </style>
</head>
<body class="p-4 sm:p-8">

    <main class="max-w-4xl mx-auto bg-white rounded-2xl shadow-xl p-6 sm:p-10 lg:p-12">
        <h1 class="text-3xl sm:text-4xl font-extrabold text-center mb-10 text-gray-900">
            AWS CloudFront Distribution Setup
        </h1>

        <!-- Step 1 -->
        <section class="mb-10">
            <h2 class="text-2xl font-bold mb-4 text-gray-800">Step 1: Prepare Your S3 Bucket</h2>
            <p class="mb-4 text-gray-700">The first step is to have an S3 bucket with the content you want to distribute.</p>
            <ul class="space-y-4 text-gray-700 list-disc list-inside">
                <li>
                    <span class="font-semibold">Create an S3 Bucket:</span> Go to the AWS Management Console, navigate to the S3 service, and create a new bucket. Choose a globally unique name for your bucket.
                </li>
                <li>
                    <span class="font-semibold">Upload Content:</span> Upload all the static content (HTML, CSS, JavaScript files, images, etc.) that you want to serve through CloudFront.
                </li>
                <li>
                    <span class="font-semibold">Configure Access:</span> Ensure that "Block all public access" is enabled for the bucket. This is a critical security measure. CloudFront will be granted access to this bucket later, but the bucket itself will remain private and inaccessible directly from the public internet.
                </li>
            </ul>
        </section>

        <!-- Step 2 -->
        <section class="mb-10">
            <h2 class="text-2xl font-bold mb-4 text-gray-800">Step 2: Create an Origin Access Control (OAC)</h2>
            <p class="mb-4 text-gray-700">An OAC is a recommended feature that securely connects your CloudFront distribution to your S3 bucket.</p>
            <ul class="space-y-4 text-gray-700 list-disc list-inside">
                <li>
                    <span class="font-semibold">Navigate to CloudFront:</span> In the AWS Management Console, go to the CloudFront service.
                </li>
                <li>
                    <span class="font-semibold">Create an OAC:</span> In the left-hand navigation pane, under <span class="font-bold text-gray-900">Security</span>, select <span class="font-bold text-gray-900">Origin access</span>. Click <span class="font-bold text-gray-900">Create control setting</span>.
                </li>
                <li>
                    <span class="font-semibold">Configure OAC:</span>
                    <ul class="ml-6 mt-2 space-y-2 list-circle list-inside">
                        <li><span class="font-semibold">Name:</span> Provide a descriptive name for your OAC (e.g., `my-s3-bucket-oac`).</li>
                        <li><span class="font-semibold">Origin type:</span> Select `S3`.</li>
                        <li>Click <span class="font-bold text-gray-900">Create</span>.</li>
                    </ul>
                </li>
                <li>
                    <span class="font-semibold">Copy the Policy:</span> After the OAC is created, CloudFront will display a bucket policy. Copy this policy to your clipboard. This policy is what grants CloudFront permission to read files from your private S3 bucket.
                </li>
            </ul>
        </section>

        <!-- Step 3 -->
        <section class="mb-10">
            <h2 class="text-2xl font-bold mb-4 text-gray-800">Step 3: Create the CloudFront Distribution</h2>
            <p class="mb-4 text-gray-700">This is where you define how CloudFront will serve your content.</p>
            <ul class="space-y-4 text-gray-700 list-disc list-inside">
                <li>
                    <span class="font-semibold">Start a New Distribution:</span> In the CloudFront console, select <span class="font-bold text-gray-900">Distributions</span> from the left-hand menu, then click <span class="font-bold text-gray-900">Create distribution</span>.
                </li>
                <li>
                    <span class="font-semibold">Configure Origin:</span>
                    <ul class="ml-6 mt-2 space-y-2 list-circle list-inside">
                        <li><span class="font-semibold">Origin domain:</span> Click in this field and select your S3 bucket from the dropdown list.</li>
                        <li><span class="font-semibold">Name:</span> The origin ID will be automatically populated.</li>
                        <li><span class="font-semibold">Origin access:</span> Choose <span class="font-bold text-gray-900">Origin access control settings (recommended)</span>.</li>
                        <li><span class="font-semibold">Origin access control:</span> Select the OAC you created in the previous step.</li>
                        <li><span class="font-semibold">Bucket policy:</span> CloudFront will prompt you to update the S3 bucket policy. Click <span class="font-bold text-gray-900">Copy policy</span> and then <span class="font-bold text-gray-900">Go to S3 bucket permissions</span>.</li>
                        <li>Paste the copied policy into the <span class="font-bold text-gray-900">Bucket policy editor</span> in your S3 bucket's permissions section and save the changes. This completes the secure connection.</li>
                    </ul>
                </li>
                <li>
                    <span class="font-semibold">Configure Default Cache Behavior:</span>
                    <ul class="ml-6 mt-2 space-y-2 list-circle list-inside">
                        <li><span class="font-semibold">Viewer protocol policy:</span> Select <span class="font-bold text-gray-900">Redirect HTTP to HTTPS</span>. This is a standard security practice.</li>
                        <li><span class="font-semibold">Allowed HTTP methods:</span> Select <span class="font-bold text-gray-900">GET, HEAD</span>. This is sufficient for static content.</li>
                        <li><span class="font-semibold">Cache policy:</span> Choose <span class="font-bold text-gray-900">CachingOptimized</span>. This managed policy is ideal for static content and ensures optimal caching.</li>
                    </ul>
                </li>
                <li>
                    <span class="font-semibold">Configure Other Settings:</span>
                    <ul class="ml-6 mt-2 space-y-2 list-circle list-inside">
                        <li><span class="font-semibold">Distribution settings:</span> You can optionally add a <span class="font-bold text-gray-900">Default root object</span> (e.g., `index.html`) so that users can access your site by just typing your domain name.</li>
                        <li><span class="font-semibold">Price class:</span> Select the price class that best suits your needs.</li>
                        <li><span class="font-semibold">WAF (Web Application Firewall):</span> You can optionally enable WAF for an extra layer of security.</li>
                    </ul>
                </li>
                <li>
                    <span class="font-semibold">Create Distribution:</span> Click <span class="font-bold text-gray-900">Create distribution</span>.
                </li>
            </ul>
        </section>

        <!-- Step 4 -->
        <section>
            <h2 class="text-2xl font-bold mb-4 text-gray-800">Step 4: Test Your CloudFront Distribution</h2>
            <ul class="space-y-4 text-gray-700 list-disc list-inside">
                <li>
                    <span class="font-semibold">Wait for Deployment:</span> CloudFront distributions take some time to deploy. The status will show as `Deploying`.
                </li>
                <li>
                    <span class="font-semibold">Get the Domain Name:</span> Once the status changes to `Deployed`, go back to the list of distributions. Copy the <span class="font-bold text-gray-900">Distribution domain name</span>. It will look like `d1234abcd.cloudfront.net`.
                </li>
                <li>
                    <span class="font-semibold">Access Your Content:</span> Paste this domain name into your browser's address bar. If you configured a default root object, you'll see your website. Otherwise, append the path to one of your files (e.g., `d1234abcd.cloudfront.net/your-image.jpg`).
                </li>
            </ul>
        </section>

    </main>

</body>
</html>
