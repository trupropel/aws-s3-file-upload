# AWS S3 File Upload

AWS S3 File Upload is a module for uploading files to S3. Unlike other libraries out there, there are no native dependencies.

```
npm install --save @trupropel/aws-s3-file-upload
```


## Note on S3 user permissions

The user associated with the `accessKey` (usually starting with AKIA prefix) and `secretKey` you use must have the appropriate permissions assigned to them.

This library also accepts temporary credentials.

 When using temporary credentials (e.g: cognito credentials) for accessing aws resources, users must provide `accessKey` (usually starting with ASIA prefix), `secretKey` and `sessionToken`.
## Example

```javascript
import { RNS3 } from '@trupropel/aws-s3-file-upload';

const file = {
  // `uri` can also be a file system path (i.e. file://)
  uri: "...",
  name: "image.png",
  type: "image/png"
}

const options = {
  keyPrefix: "uploads/",
  bucket: "your-bucket",
  region: "your-region",
  accessKey: "your-access-key",
  secretKey: "your-secret-key",
  sessionToken: "your-session-token", //optional
  successActionStatus: 201
}

RNS3.put(file, options).then(response => {
  if (response.status !== 201)
    throw new Error("Failed to upload image to S3");
  console.log(response.body);
});
```

## Usage

### put(file, options)

Upload a file to S3.

Arguments:

1. `file`
  * `uri` **required** - File system URI, can be assets library path or `file://` path
  * `name` **required** - The name of the file, will be stored as such in S3
  * `type` **required** - The mime type, also used for `Content-Type` parameter in the S3 post policy
2. `options`
  * `acl` - The [Access Control List](http://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html) of this object. Defaults to `public-read`
  * `keyPrefix` - Prefix, or path to the file on S3, i.e. `uploads/` (note the trailing slash)
  * `bucket` **required** - Your S3 bucket
  * `region` **required** - The region of your S3 bucket
  * `accessKey` **required** - Your S3 `AWSAccessKeyId` (can be a temporary accessKey, use with sessionToken)
  * `secretKey` **required** - Your S3 `AWSSecretKey` (can be a temporary secretKey, use with sessionToken)
  * `sessionToken` - Your sessionToken
  * `successActionStatus` - HTTP response status if successful, defaults to 201
  * `awsUrl` - [AWS S3 url](http://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region). Defaults to `s3.amazonaws.com`
  * `timeDelta` - Devices time offset from world clock in milliseconds, defaults to 0

Returns an object that wraps an `XMLHttpRequest` instance and behaves like a promise, with the following additional methods:

* `progress` - accepts a callback which will be called with an event representing the progress of the upload. Event object is of shape
  * `loaded` - amount uploaded
  * `total` - total amount to upload
  * `percent` - number between 0 and 1 representing the percent completed
* `abort` - aborts the xhr instance
