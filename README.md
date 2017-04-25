Django authentication backend using Atlassian Crowd
=====================================================

This is a very rough implementation of a Django backend using [Atlassian Crowd](https://www.atlassian.com/software/crowd)'s
REST API.

It is inspired by the various forks of `django-crowd-backend`.
Those implementations were always using SOUP.
This one is just using REST API supported by Crowd.
See [Atlassian documentation](https://developer.atlassian.com/display/CROWDDEV/Crowd+REST+APIs) for more information on this API.

Current implementation
======================

- Connect to an [Atlassian Crowd](https://www.atlassian.com/software/crowd) server
- Authenticate given user by password
- Sync Django user instance with attributes from Crowd user
- Setup Django user staff/superuser flags based on associated Crowd groups of user

Features
========

- HTTPS certificate validation when connecting to secure Crowd URL

Missing
=======

- No handling of SSO cookie

Dependencies
============

- just 'urllib2' with a little tweak from [VerifiedHTTPS](https://github.com/josephturnerjr/urllib2.VerifiedHTTPS)
  to allow validation of https certificate.

How to use it
=============

- Edit `settings.py` to add `crowdrest` app to your list of apps

- Adapt configuration settings for `crowdrest` in `settings.py` by adding

  * whether you want to sync django users from Crowd attributes
    ```
    AUTH_CROWD_ALWAYS_UPDATE_USER = True
    ```
  * whether you want to sync django groups from Crowd groups
    ```
    AUTH_CROWD_ALWAYS_UPDATE_GROUPS = True
    ```

    If you use any form of group-based autorization/permission checking,
    you'd rather have this as True (default). In particular, `AUTH_CROWD_STAFF_GROUP`
    & `AUTH_CROWD_SUPERUSER_GROUP` settings depend on this.

  * whether you want to sync all user's Crowd groups into Django
    ```
    AUTH_CROWD_CREATE_GROUPS = False
    ```

    This setting is considered only if `AUTH_CROWD_ALWAYS_UPDATE_GROUPS = True`.

    * If this is `True`, then all user's groups in Crowd will be synced to Django (so,
      effectively, you'll be able to check Crowd group memberships using Django API)
    * If set to `False` (default), no groups will be created by backend, and only groups
      already existing in Django will be considered (i.e. user group membership in
      Django will be updated to intersection of user's Crowd groups and all available
      Django groups).

    You'd rather have this as `True` (default). In particular, `AUTH_CROWD_STAFF_GROUP`
    & `AUTH_CROWD_SUPERUSER_GROUP` settings depend on this.

  * Django user will get staff flag when Crowd user is in given Crowd group
    ```
    AUTH_CROWD_STAFF_GROUP = 'staff'
    ```

  * Django user will get superuser flag when Crowd user is in given Crowd group
    ```
    AUTH_CROWD_SUPERUSER_GROUP = 'superuser'
    ```

    Note that superuser group member does not imply staff membership and vice
    versa (make sure you read Django docs to understand the difference).

  * crowdrest will use this username and password to connect to Crowd server
    ```
  	AUTH_CROWD_APPLICATION_USER = 'django'
  	AUTH_CROWD_APPLICATION_PASSWORD = 'django'
    ```

  * URL to Crowd REST API
    ```
    AUTH_CROWD_SERVER_REST_URI = 'http://127.0.0.1:8095/crowd/rest/usermanagement/latest'
    ```

  * Use given certificate file to validate https connection to Crowd server
    ```
    AUTH_CROWD_SERVER_TRUSTED_ROOT_CERTS_FILE = None
    ```

Problems ?
==========

Just send me a message. Let's see if I can help.

License
=======
Use this code as you want. Consider it free. Say thank you. Don't blame me if it doesn't work for you.
