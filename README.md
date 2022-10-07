# CI Badges action

![version](https://img.shields.io/github/package-json/v/gaelgirodon/ci-badges-action?style=flat-square)
![license](https://img.shields.io/github/license/gaelgirodon/ci-badges-action?style=flat-square)
![build](https://img.shields.io/github/workflow/status/gaelgirodon/ci-badges-action/main?style=flat-square)
![tests](https://img.shields.io/endpoint?style=flat-square&url=https%3A%2F%2Fgist.githubusercontent.com%2FGaelGirodon%2F715c62717519f634185af0ebde234992%2Fraw%2Fci-badges-action-junit-tests.json)
![coverage](https://img.shields.io/endpoint?style=flat-square&url=https%3A%2F%2Fgist.githubusercontent.com%2FGaelGirodon%2F715c62717519f634185af0ebde234992%2Fraw%2Fci-badges-action-cobertura-coverage.json)

This action generates badges (as JSON files) from Go, JUnit, Cobertura and
JaCoCo test and coverage reports and upload them to a Gist to make them
available to Shields through the endpoint feature with (almost) zero
configuration.

## Usage

Create a Gist and an access token (with the `gist` scope) and store the access
token in your repository secrets.

Add the action to your workflow (after running tests, as reports must be
available in the current working directory):

```yaml
uses: gaelgirodon/ci-badges-action@v1
with:
  gist-id: <gist-id>
  token: ${{ secrets.GIST_TOKEN }}
```

Each generated badge JSON file is uploaded to your Gist and is available
via: `https://gist.githubusercontent.com/{user}/{gist-id}/raw/{repo}-[{ref}-]{format}-{type}.json`,
e.g. `.../myproject-cobertura-coverage.json`.

This URL can be used with [Shields Endpoint](https://shields.io/endpoint)
to generate the badge SVG image: `https://img.shields.io/endpoint?url={url}`.
The usual query parameters (`style`, `label`, ...) can be used to further
customize the badge.

## Inputs

| Name      | Required | Default         | Description                                             |
| --------- | :------: | --------------- | ------------------------------------------------------- |
| `gist-id` |    ✔️     |                 | The unique identifier of the Gist                       |
| `token`   |    ✔️     |                 | GitHub REST API access token (with the `gist` scope)    |
| `prefix`  |          | Repository name | Filenames prefix                                        |
| `ref`     |          | `false`         | Include the short ref name (branch or tag) in filenames |

## Badges

This action loads reports, extracts key data and generates JSON files following
the Shields schema, instead of directly generating SVG images, to allow taking
advantage of Shields customization features (through the query string).

### Tests

![tests](https://img.shields.io/endpoint?style=flat-square&url=https%3A%2F%2Fgist.githubusercontent.com%2FGaelGirodon%2F715c62717519f634185af0ebde234992%2Fraw%2Frepo-go-tests.json)
![tests](https://img.shields.io/endpoint?style=flat-square&url=https%3A%2F%2Fgist.githubusercontent.com%2FGaelGirodon%2F715c62717519f634185af0ebde234992%2Fraw%2Frepo-junit-tests.json)

This badge displays the number of passed and failed tests extracted from a test
report.

```json
{"schemaVersion":1,"label":"tests","message":"3 passed","color":"brightgreen"}
```

### Coverage

![coverage](https://img.shields.io/endpoint?style=flat-square&url=https%3A%2F%2Fgist.githubusercontent.com%2FGaelGirodon%2F715c62717519f634185af0ebde234992%2Fraw%2Frepo-go-coverage.json)
![coverage](https://img.shields.io/endpoint?style=flat-square&url=https%3A%2F%2Fgist.githubusercontent.com%2FGaelGirodon%2F715c62717519f634185af0ebde234992%2Fraw%2Frepo-cobertura-coverage.json)
![coverage](https://img.shields.io/endpoint?style=flat-square&url=https%3A%2F%2Fgist.githubusercontent.com%2FGaelGirodon%2F715c62717519f634185af0ebde234992%2Fraw%2Frepo-jacoco-coverage.json)

This badge displays the percentage of covered lines extracted from a coverage
report.

```json
{"schemaVersion":1,"label":"coverage","message":"96%","color":"brightgreen"}
```

## Report formats

This action supports multiple report formats with their conventional or most
popular configurations.

Only matched report formats will get a file uploaded to the Gist.

### Go

Write the verbose test output (`>` or `tee`) with coverage enabled to a
`test*.{out,txt}` file next to the `go.mod` file:

- `RUN`, `PASS` and `FAIL` flags will be used to count tests
- The last percentage will be used as the coverage value

`go tool cover -func=cover.out` output may be appended to the above file to make
sure the last percentage is the global coverage value.

➡️ `{repo}-[{ref}-]go-tests.json`<br>
➡️ `{repo}-[{ref}-]go-coverage.json`

### JUnit

Write the test report to a file matching:

- `**/report.xml`
- `**/*TEST*.xml`
- `**/*test*.xml`
- `**/*junit*.xml`

The number of tests and failures will be extracted from the `<testsuites>` tag.

➡️ `{repo}-[{ref}-]junit-tests.json`

### Cobertura

Write the coverage report to a file matching:

- `**/*cobertura*.xml`
- `**/*coverage*.xml`

The coverage will be extracted from the `line-rate` attribute of the
`<coverage>` tag.

➡️ `{repo}-[{ref}-]cobertura-coverage.json`

### JaCoCo

Write the coverage report to a file matching:

- `**/*jacoco*.xml`
- `**/*coverage*.xml`

The coverage will be extracted from the last `<counter>` tag with type `LINE`.

➡️ `{repo}-[{ref}-]jacoco-coverage.json`

## License

**CI Badges action** is licensed under the GNU General Public License.
