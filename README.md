Django authentication backend using Atlassian Crowd
=====================================================

This is a very rough implementation of a Django backend using [Atlassian Crowd](https://www.atlassian.com/software/crowd)'s
REST API.
See [Atlassian documentation](https://developer.atlassian.com/display/CROWDDEV/Crowd+REST+APIs) for more information on this API.

This is a fork of the original implementation https://github.com/Linaro/django-crowd-rest-backend with various improvements:

* letting Users stay superusers or staff
* Fix Django > 1.10 compatibility issues
* Removing unnecessary model change
* Various code improvements (PEP8, etc)

Current implementation
======================

- Connect to an [Atlassian Crowd](https://www.atlassian.com/software/crowd) server
- Authenticate given user by password
- Sync Django user instance with attributes from Crowd user
- Setup Django user staff/superuser flags based on associated Crowd groups of user

Features
========

* Synchronization of groups from Crowd
* SuperUser and Staff groups handling
* HTTPS certificate validation when connecting to secure Crowd URL

Missing
=======
* No handling of SSO cookie

Dependencies
============
No dependencies outside of Django and Python.

How to use it
=============

1. Edit `settings.py` to add `crowdrest` app to your list of apps
1. Adapt configuration settings for `crowdrest` in `settings.py` using the attributes detailed below.

Crowdrest configuration
-----------------------

* indicates whether you want to sync Django users from Crowd attributes:
  ```
  # Defaults to True
  AUTH_CROWD_ALWAYS_UPDATE_USER = True
  ```
* indicates whether you want to sync django groups from Crowd groups:
  ```
  # Defaults to True
  AUTH_CROWD_ALWAYS_UPDATE_GROUPS = True
  ```

  If you use any form of group-based authorization/permission checking,
  you'd rather have this as `True` (default). In particular, `AUTH_CROWD_STAFF_GROUP`
  & `AUTH_CROWD_SUPERUSER_GROUP` settings depend on this.

* indicates whether you want to sync all user's Crowd groups into Django
  ```
  # Defaults to False
  AUTH_CROWD_CREATE_GROUPS = False
  ```

  This setting is considered only if `AUTH_CROWD_ALWAYS_UPDATE_GROUPS = True`.

  * If this is `True`, then all user's groups in Crowd will be synced to Django (so,
    effectively, you'll be able to check Crowd group memberships using Django API)
  * If set to `False` (default), no groups will be created by the backend, and only groups
    already existing in Django will be considered (i.e. user group membership in
    Django will be updated to intersection of user's Crowd groups and all available
    Django groups).

  You'd rather have this as `True`. In particular, `AUTH_CROWD_STAFF_GROUP`
  and `AUTH_CROWD_SUPERUSER_GROUP` settings depend on this.

* Django user will get `staff` flag when Crowd user is in given Crowd group
  ```
  # Defaults to None
  AUTH_CROWD_STAFF_GROUP = 'staff'
  ```

* Django user will get `superuser` flag when Crowd user is in given Crowd group
  ```
  # Defaults to None
  AUTH_CROWD_SUPERUSER_GROUP = 'superuser'
  ```

  Note that superuser group member does not imply staff membership and vice
  versa (make sure you read Django docs to understand the difference)

* indicates if you want the `superuser` and `staff` be overridden at every login:
  ```
  # defaults to False
  AUTH_CROWD_ALWAYS_UPDATE_SUPERUSER_STAFF_STATUS = False
  ```

  If set to `True`, the authentication backend will check if the user belongs to
  the groups in `AUTH_CROWD_STAFF_GROUP` and/or `AUTH_CROWD_SUPERUSER_GROUP` and
  will update the user accordingly, *always*. This overrides the corresponding properties
  of the User, even if the User was set eg. Staff from the Django Admin interface.
  Set this option to `False` if those properties should not be overridden.

  At first login (when the User is created in Django after the first successful login)
  the `staff` and `superuser` flags are set according to `AUTH_CROWD_STAFF_GROUP` and
  `AUTH_CROWD_SUPERUSER_GROUP` respectively, if those exists.

* Crowdrest will use this username and password to connect to Crowd server
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

Tweaks
======
HTTPS certificate validation uses [VerifiedHTTPS](https://github.com/josephturnerjr/urllib2.VerifiedHTTPS) tweak.


License
=======
Use this code as you want. Consider it free. Say thank you. Don't blame me if it doesn't work for you.
