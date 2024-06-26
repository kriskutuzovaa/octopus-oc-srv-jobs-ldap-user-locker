# LDAP USER LOCKER

## Goal

Lock LDAP user accounts automatically by some reason. To be used as scheduled task.

## Limitations:

Depends on *oc_ldap_client*, so all its limitations are actual here.

## Configuration
To be written separatley and provided with _--config_ argument. Is a JSON file.
Example:

```
{
    "LDAP": {
        "url": "ldap://ldap-test.example.local",
        "user_cert": "/home/user/ssl/test/user.pem",
        "user_key": "/home/user/ssl/test/user.priv.key",
        "ca_chain": "/home/user/ssl/test/CA.chain.pem",
        "baseDn": "dc=domain,dc=example,dc=local"
    },
    "SMTP": {
        "url": "smtp://test.smtp.example.com:25",
        "user": "TEST_USER",
        "password": "TEST_PASSWORD",
        "from": "testuser@test.example.com",
        "subject": "default e-mail subject"
    },
    "users": [
		{
            "days_valid": 730, 
            "time_attributes": ["authTimestamp", "modifyTimeStamp", "createTimestamp"],
			"condition_attributes": {   
                "memberOf.businessCategory": {
                    "values": [
                        "Vendor"
                    ]
                }
            },
            "lock_notifications": [
                {
                    "days_before": 30,
                    "template": { 
                        "file": "default_en.html.template",
                        "type": "html",
                        "signature": "signature.png"
                    }
                },
                {
                    "days_before": 10,
                    "template": { 
                        "file": "default_en.html.template",
                        "type": "html",
                        "signature": "signature.png"
                    }
                }
            ]
        },
		{
            "days_valid": 90, 
            "time_attributes": ["authTimestamp", "modifyTimeStamp", "createTimestamp"],
            "lock_notifications": [
                {
                    "days_before": 30,
                    "template": { 
                        "file": "default_en.html.template",
                        "type": "html",
                        "signature": "signature.png"
                    }
                },
                {
                    "days_before": 10,
                    "template": { 
                        "file": "default_en.html.template",
                        "type": "html",
                        "signature": "signature.png"
                    }
                }
            ]
        },
        {
            "days_valid": 0, 
            "time_attributes": ["modifyTimeStamp", "createTimestamp"], 
            "condition_attributes": 
            {
                "mail": {
                    "comparison": {
                        "type": "regexp",
                        "condition": "any"
                    },
                    "values": [
                        ".*@gmail\\.[a-z]+", 
                        ".*@mail\\.[a-z]+", 
                        ".*@inbox\\.ru",
                        ".*@yandex\\.ru",
                        ".*@yahoo(mail|\\-inc)?\\.[a-z]+",
                        ".*@ymail\\.[a-z]+",
                        ".*@rocketmail\\.[a-z]+",
                        ".*@hotmail\\.[a-z]+",
                        ".*@rambler\\.ru",
                        ".*@qip\\.ru",
                        ".*@bigmir\\.net",
                        ".*@ukr\\.net",
                        ".*@usa\\.net",
                        ".*@live\\.[a-z]+",
                        ".*@msn\\.[a-z]+",
                        ".*@googlemail\\.[a-z]+"
                    ]
                }
            }
        }
    ]
}

```
Possible values _comparison_ sub-parameters:
    * _type_: **regexp**, **plain** (default)
    * _condition_: **all** (default), **any**

If _type_ is **regexp** then _Python_ regular expressions are required in _values_ section.
Non-string attributes comparison is not supported.
All comparisons are case-insensitive.

### E-mail subject
    - from notification configuration
    - from global **SMTP** section if missing in template settings
    - "Account lock warning" by default if both above missing

## Mail template substitutes supported

    * *cn* - user login
    * *givenName* - user first name
    * *sn* - user last name
    * *displayName* - user display name
    * *lockDate* - locking date ('YYYY-MM-DD')
    * *lockDays* - days before locking

## Which configuration section is used
Up to v. 1.1.0: that one which has less `days_valid` value.
Since v. 1.2.0: that one whicn has more strict filter correspondence. If amount of attributes matched is equal then first one comes with a configuration is used.
