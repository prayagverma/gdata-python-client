# Test Files #

All of the unit tests and integration tests live in the /tests directory in the download. Each test can be run individually.

There are also scripts which will run all tests. One for the v1 services and another for the multiversion services.

For the new tests run `all_tests.py`

To run the legacy v1 service tests, use `run_all_tests.py`


# Running #

The integration tests require a username, password, and other service specific information (such as a spreadsheet key for a test spreadsheet). These arguments may be supplied when prompted or using command line arguments. Here are some examples:

## Local tests ##

There are lightweight tests which do not communicate with the remote servers. You can run these using

`all_tests_local.py` for the multiversion tests and
`run_data_tests.py` for the legacy v1 tests.

## Multiversion tests ##

These tests make real requests to the remote servers.

```
$ ./all_tests.py --username <your-email> \
--password=<your-password> \
--blogid <test-blog-id> \
--imgpath <path-to-an-image-to-upload> \
--spreadsheetid=<spreadsheet-key-like-t9cICNNer921QKVjYPybVjg> \
--sitename=<name-of-test-google-site-like-testingonly123site>  \
--project_name=<test-open-source-project> \
--issue_assignee=<test-project-member-email> \
--table_id <analytics-account-id-like-ga:12312312> \
--appsdomain <domain-name-of-test-google-apps-domain> \
--appsusername=<admin-email-for-test-domain> \
--appspassword=<admin-passwd-for-test-domain> \
--ssl=true
```

Note that if you supply `--appsusername`, then the `--sitename` value must refer to a Site in the given Apps domain.

The responses are cached and so repeated runs can be told to use cached responses to speed up runs. To use the cached responses you can run `all_tests_cached.py` instead of `all_tests.py`.

The `all_tests_cached.py` script is equivalent to adding the following to the command line

`--runlive true --savecache true --clearcache false`

For more information on how these tests work. Take a look at `src/gdata/test_config.py`

## Legacy tests ##

These tests make real requests to the remote servers.

`./run_all_tests.py --username <your-email> --password <your-password> --ss_key <spreadsheet-key-like-t9cICNNer921QKVjYPybVjg>  --ws_key <spreadsheet-worksheet-id-like-1> --apps_username <test-domain-admin-email> --apps_domain <domain-name-of-test-google-apps-domain> --apps_password <password-for-test-domain-admin>`