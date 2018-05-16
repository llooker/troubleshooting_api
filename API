# Looker SDK

The Looker SDK repository is the gathering place for all of Looker's reference implementations for the Looker API.

Our goal is to lower barriers to entry for customers to use the Looker API as a data platform, largely by providing pre-built client SDKs in the most popular languages, and curating consistency across all languages and platforms.

The Looker API is defined with [OpenAPI specification](https://github.com/OAI/OpenAPI-Specification). This specification is used to produce both Looker's interactive API Explorer, and the Looker API language bindings.

In most cases, the Looker API bindings for a specific language are generated with [Swagger Code-gen](https://github.com/swagger-api/swagger-codegen).

There are existing API/SDK implementations that will be considered and/or integrated as we develop the SDKs in this repository:

* [Looker Ruby SDK](https://github.com/looker/looker-sdk-ruby)
* [Looker Java SDK](https://github.com/looker/looker-sdk-java) uses Swagger Codegen to produce the API, and includes a sample application showing how to use it.
* Python https://github.com/llooker/python_api_samples obsolete?
* Python fork https://github.com/llooker/jt_python_api unknown status
* TypeScript https://github.com/looker/looker-sdk-js is obsolete. Helltool now generates a TypeScript API binding every time it builds via https://github.com/looker/swagger-codegen-typescript. This output is a cleaner implementation of the Looker API, used via node as well (eventually).
* API compliance testing https://github.com/looker/ruby_api_minitest_via_java_sdk
* R https://github.com/looker/lookr

## The parts of the Looker SDK

The Looker SDK is comprised of several parts:

* **Looker API** OpenAPI specification (found at
  `https://<your-looker-endpoint>:19999/api/3.0/swagger.json`)
* The **Looker API Explorer**, generated in the Looker web app directly from  `swagger.json`
* **API bindings**, generated for each language from `swagger.json` via Swagger Code-gen
* **Language SDKs**, manually written to accelerate usage of the API binding

## Generating an API language binding

[Generating Client SDKs for the Looker API](https://discourse.looker.com/t/generating-client-sdks-for-the-looker-api/3185) describes the manual steps for generating an API language binding. In this repository, [`prepare.sh`](prepare.sh) automates these steps.

If the language you use isn't provided in this repository, you can follow the manual steps in the article above, or modify the [`target_languages.txt`](target_languages.txt) file to add it.

## Using `prepare.sh` to automate API binding generation

[`prepare.sh`](prepare.sh) automates the manual steps describe in [Generating Client SDKs for the Looker API](https://discourse.looker.com/t/generating-client-sdks-for-the-looker-api/3185).

This script will read the API configuration values from a file called `looker.ini`. By default, the configuration file needs to be in the same folder as `prepare.sh`.

To create `looker.ini`, copy [`looker-sample.ini`](looker-sample.ini) to `looker.ini` and fill in the required values.

All SDKs use this `.ini` format to save/retrieve their API configuration settings.

### `prepare.sh` parameters

You can invoke [`prepare.sh`](prepare.sh) with:

```bash
sh prepare.sh [option]
```

where `option` is:

* `clean`: remove generated `api/*` folders
* `wipe`: remove the `swagger-codegen` folder in addition to `clean`
* `file`: configuration file name to use instead of `looker.ini`

Example:

```bash
sh prepare.sh ~/myconfig.ini
```

Will read API configuration values from `~/myconfig.ini` rather than `looker.ini`

### automated processing

`prepare.sh` is a bash shell script that:

1. (optionally) retrieves the Looker API specification file from the Looker server
2. If `swagger-codegen` isn't found in the current path, it ses `git` to retrieve the `swagger-codegen` project at our supported version (currently 2.3.1) and build it
3. Reads [`target_languages.txt`](target_languages.txt) to determine which languages to process, including special generation flags for those languages. (Please refer to the [Swagger Code-gen](https://github.com/swagger-api/swagger-codegen) homepage for the list of supported languages.)
4. Generates each requested language binding declared in [`target_languages.txt`](target_languages.txt) into `api/language_path`

When `prepare.sh` completes successfully, the directory structure will look similar to:

```plain-text
api (created by `prepare.sh`. This and its subfolders can be recreated with it.)
  csharp
  java
  kotlin
  r
  ruby
  rust
  typescript_fetch
  typescript_node
sdk (the directory containing the various language SDKs)
swagger-codegen (created by `prepare.sh`)
```

## API Troubleshooting

There are a few simple checks to make when you encounter some common API problems.

### API endpoint isn't reachable

If you can't reach any of the API endpoints, here are some things to check:

* Go to Admin | API
  * Click `View API docs`
    * If the API Explorer appears,
    * Provide the API3 `client_id` and `client_secret` and click `Log In`
    * Open the `User : Manage Users` section
    * Expand the `Me` API call
    * Click `Try It!`
    * This should produce a `200` **Response Code** with a **Response body**
      that starts with something like:
      ```json
      {
        "id": 1,
        "first_name": "Luke",
        "last_name": "Ahr",
        "email": "test@looker.com",
        "is_disabled": false,
        ...
      ```
    * If the expected `json` output is shown, access to the API using the
      same `client_id` and `client_secret` should work.
  * If the above steps don't resolve the problem, verify that `API Host URL`
    is either completely blank, or correctly specified. The Looker server
    will attempt to automatically determine the host URL based on the current
    instance. If it can't, value should be explicitly set.
* Finally, open a browser.
  * Enable the browser debugger.
  * Open `https://<your-looker-hostname>:19999/alive`
  * This should produce a blank page, but not an error message.
  * Check the network response. If the browser response is `200` you can reach the API.


If these checks fail, it is likely that port `19999` needs to be opened on the network infrastructure.
The API port should forward to the Looker server. The configuration should be the same as it is for
port `9999` (the default port). Check:

* proxies
* load balancers
* firewalls

### API calls hang

If the API explorer can be viewed but API calls hang, verify the Admin|API page's
`API Host URL` value is either blank or set correctly.

### Login returns `404`

If API login attempts are returning `404` errors, the `client_id` or `client_secret` is probably wrong.
The API login REST endpoint is `https://<your-looker-hostname>:19999/api/3.0/login` or
`https://<your-looker-hostname>:19999/api/3.1/login`, depending on the API version in use.

If using a Swagger codegen API or a Looker SDK, ensure your `base_url` value is correct.

* For a Swagger codegen client, the `base_url` should be `https://<your-looker-hostname>:19999/api/3.0/` or
`https://<your-looker-hostname>:19999/api/3.1/`, depending on the API version in use
* For **Looker SDK** implementations that use a `looker.ini`, the values should be:

```ini
# api_version should be either 3.0 or 3.1
api_version=3.0
base_url=https://<your-looker-hostname>:19999
```
