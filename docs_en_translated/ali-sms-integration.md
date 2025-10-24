# Alibaba Cloud SMS Integration Guide

Log in to the Alibaba Cloud console and go to the "SMS Service" page: https://dysms.console.aliyun.com/overview

## Step 1: Add Signature
![Steps](images/alisms/sms-01.png)
![Steps](images/alisms/sms-02.png)

After the above steps, you will get a signature. Please write it to the control panel parameters: `aliyun.sms.sign_name`

## Step 2: Add Template
![Steps](images/alisms/sms-11.png)

After the above steps, you will get a template code. Please write it to the control panel parameters: `aliyun.sms.sms_code_template_code`

**Note**: The signature needs to wait for 7 working days, and SMS can only be sent successfully after the operator registration is successful.

**Note**: The signature needs to wait for 7 working days, and SMS can only be sent successfully after the operator registration is successful.

**Note**: The signature needs to wait for 7 working days, and SMS can only be sent successfully after the operator registration is successful.

You can continue with the following operations after the registration is successful.

## Step 3: Create SMS Account and Enable Permissions

Log in to the Alibaba Cloud console and go to the "Access Control" page: https://ram.console.aliyun.com/overview?activeTab=overview

![Steps](images/alisms/sms-21.png)
![Steps](images/alisms/sms-22.png)
![Steps](images/alisms/sms-23.png)
![Steps](images/alisms/sms-24.png)
![Steps](images/alisms/sms-25.png)

After the above steps, you will get access_key_id and access_key_secret. Please write them to the control panel parameters: `aliyun.sms.access_key_id`, `aliyun.sms.access_key_secret`

## Step 4: Enable Mobile Registration Function

1. Normally, after filling in all the above information, you should see this effect. If not, some step might be missing.

![Steps](images/alisms/sms-31.png)

2. Enable allowing non-admin users to register by setting the parameter `server.allow_user_register` to `true`

3. Enable mobile registration function by setting the parameter `server.enable_mobile_register` to `true`
![Steps](images/alisms/sms-32.png)

## Configuration Parameters

After completing all the above steps, you need to configure the following parameters in your system:

### Required Parameters
- `aliyun.sms.sign_name`: SMS signature obtained from Step 1
- `aliyun.sms.sms_code_template_code`: Template code obtained from Step 2
- `aliyun.sms.access_key_id`: Access key ID obtained from Step 3
- `aliyun.sms.access_key_secret`: Access key secret obtained from Step 3

### System Parameters
- `server.allow_user_register`: Set to `true` to allow user registration
- `server.enable_mobile_register`: Set to `true` to enable mobile registration

## Testing the Integration

1. **Verify Configuration**: Ensure all parameters are correctly set
2. **Test SMS Sending**: Try sending a test SMS to verify the integration
3. **Check Registration Flow**: Test the complete user registration process
4. **Monitor Logs**: Check system logs for any SMS-related errors

## Troubleshooting

### Common Issues

1. **Signature Not Approved**: Wait for the 7-day approval process
2. **Template Not Approved**: Ensure template content meets Alibaba Cloud requirements
3. **Access Key Issues**: Verify access key permissions and validity
4. **Rate Limits**: Check if you've exceeded SMS sending limits

### Error Messages

- **Signature Error**: Check if the signature is approved and correctly configured
- **Template Error**: Verify template code and content
- **Authentication Error**: Check access key credentials
- **Permission Error**: Ensure proper IAM permissions are set

## Best Practices

1. **Security**: Keep access keys secure and rotate them regularly
2. **Monitoring**: Set up monitoring for SMS delivery rates and failures
3. **Rate Limiting**: Implement appropriate rate limiting for SMS sending
4. **Error Handling**: Implement proper error handling for SMS failures
5. **Logging**: Maintain detailed logs for SMS operations

## Cost Considerations

- **SMS Pricing**: Understand Alibaba Cloud SMS pricing structure
- **Volume Planning**: Plan SMS volume based on expected usage
- **Cost Optimization**: Use appropriate SMS types for different use cases
- **Budget Monitoring**: Set up cost alerts and monitoring

**Note: This integration guide may be outdated. Please refer to the latest Alibaba Cloud SMS documentation for current API changes and configuration options.**

## Additional Resources

- [Alibaba Cloud SMS Documentation](https://help.aliyun.com/product/44282.html)
- [SMS API Reference](https://help.aliyun.com/document_detail/101414.html)
- [Best Practices Guide](https://help.aliyun.com/document_detail/101415.html)
- [Troubleshooting Guide](https://help.aliyun.com/document_detail/101416.html)
