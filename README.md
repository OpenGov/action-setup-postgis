# setup-postgis

[![GitHub](https://img.shields.io/badge/github-nyurik/action--setup--postgis-8da0cb?logo=github)](https://github.com/OpenGov/action-setup-postgis)
[![CI build](https://github.com/nyurik/action-setup-postgis/actions/workflows/ci.yml/badge.svg)](https://github.com/OpenGov/action-setup-postgis/actions)
[![Marketplace](https://img.shields.io/badge/market-setup--postgis-6F42C1?logo=github)](https://github.com/marketplace/actions/setup-postgresql-and-postgis-for-linux-macos-windows)

> [!TIP]
>
> PostgreSQL installation is done by the `ikalnytskyi/action-setup-postgres` action.  All parameters are passed as is to the original action, with the addition of the `cached-dir` parameter to specify where to download and cache PostGIS binaries. See the [original documentation](https://github.com/ikalnytskyi/action-setup-postgres) for more details.

* Runs on all Linux, macOS and Windows GitHub runners
* Installs the correct version of PostGIS and runs `CREATE EXTENSION postgis` in the new database.
  * Linux version is installed from the [PostGIS apt repository](https://postgis.net/install/).
  * Windows version is installed from the [OSGeo](https://download.osgeo.org/postgis/windows/).
  * MacOS version is installed using [Homebrew package](https://formulae.brew.sh/formula/postgis).
    * MacOS only supports Postgres 14 and Postgres 17.

See also [action-setup-nginx](https://github.com/nyurik/action-setup-nginx) to configure NGINX service.

## Usage

```yaml
steps:
  - uses: nyurik/action-setup-postgis@v2
    id: postgres

  - name: Test PostGIS is installed
    run: psql -v ON_ERROR_STOP=1 -c 'SELECT PostGIS_Full_Version();' "$CONNECTION_STR"
    env:
      CONNECTION_STR: ${{ steps.postgres.outputs.connection-uri }}
```

#### Input parameters

| Key                     | Value                                                                                      | Default                                                                                                                                                                               |
|-------------------------|--------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| username                | The username of the user to setup.                                                        | `postgres`                                                                                                                                                                            |
| password                | The password of the user to setup.                                                        | `postgres`                                                                                                                                                                            |
| database                | The database name to setup and grant permissions to created user.                         | `postgres`                                                                                                                                                                            |
| port                    | The server port to listen on. ARM64 runner collide on 5432                                | `5432`                                                                                                                                                                                |
| postgres-version        | The PostgreSQL version to install. MacOS only supports 14 and 17                          | `17`                                                                                                                                                                                  |
| postgis_version         | The PostGIS version to install.                                                           | By default (empty), will use the latest. See available versions [here](https://download.osgeo.org/postgis/windows/). If set, must use the entire version string like `3.3.3`       |
| cached-dir              | Where should the temporary downloads be placed. Used to download and cache PostGIS binary. | `${{ runner.temp }}/setup-postgis-downloads`                                                                                                                                          |
| import-schema           | Import database schema.                                                                    | `false`                                                                                                                                                                               |
| schema-file             | Path to the SQL schema file to import (relative to workspace or absolute path).           | `""` (empty)                                                                                                                                                                          |
| setup-odbc              | Configure ODBC drivers.                                                                    | `false`                                                                                                                                                                               |
| search-path             | Default schema search path for database and user.                                         | `""` (empty)                                                                                                                                                                          |
| additional-extensions   | Comma-separated list of additional PostgreSQL extensions to install (beyond PostGIS).     | `""` (empty)                                                                                                                                                                          |

#### Outputs

| Key                        | Description                                      | Example                                                                                                                                                                           |
|----------------------------|--------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| connection-uri             | The connection URI to connect to PostgreSQL.    | `postgresql://postgres:postgres@localhost/postgres`                                                                                                                              |
| service-name               | The service name with connection parameters.    | `postgres`                                                                                                                                                                        |
| npgsql-connection-string   | Npgsql connection string with search path.      | `Host=localhost;Port=5432;Database=postgres;Username=postgres;Password=postgres;Search Path=;`                                                                                   |
| odbc-connection-string     | ODBC connection string.                          | `Driver={PostgreSQL ANSI};Servername=localhost;Port=5432;Database=postgres;Username=postgres;Password=postgres;`                                                                |

#### User permissions

| Key         | Value |
|-------------|-------|
| usesuper    | true  |
| usecreatedb | true  |

## License

The scripts and documentation in this project are released under the
[MIT License](LICENSE).
