[comment]: # (Set the theme:)
[comment]: # (THEME = white)
[comment]: # (CODE_THEME = shades-of-purple)
[comment]: # (The list of themes is at https://revealjs.com/themes/)
[comment]: # (The list of code themes is at https://highlightjs.org/)

Sławek Figiel | ISC | November 3, 2022

# Stork testing

[comment]: # (!!!)

## Agenda

1. Unit tests
2. Static tests
3. System tests
   1. Architecture
   2. Simple test
   3. Configuration management
   4. Features
   5. Missing parts
4. Storybook as test framework
5. Next steps

[comment]: # (!!!)

## Unit tests - Backend

- Written in Golang
- Require Postgres (standalone or in Docker)
- Server / Agent / Database / REST API
- Files with `_test.go` suffix
- Executed by `rake unittest:backend`

[comment]: # (!!!)

### Unit test example

```go [1|2|3-5|7-8|10-12]
// Collector construction fails if interval is missing.
func TestCollectorWithMissingInterval(t *testing.T) {
	// Arrange
	db, _, teardown := dbtest.SetupDatabaseTestCase(t)
	defer teardown()

	// Act
	collector, err := NewCollector(db)

	// Assert
	require.Nil(t, collector)
	require.Error(t, err)
}
```

[comment]: # (!!!)

## Unit tests - Frontend

- Written in TypeScript (Karma & Jasmine)
- Require Chrome
- Frontend logic / UI
- Files with `.spec.ts` suffix
- Executed by `rake unittest:ui`

[comment]: # (!!!)

### Unit test example

```ts [1|3|4-7|9|10|12-13]
// ... some configurations above ...

it('should not open a tab on error', () => {
    spyOn(dhcpApi, 'getHost').and.returnValue(
        throwError({ status: 404 })
    )
    spyOn(messageService, 'add')

    paramMapSubject.next(convertToParamMap({ id: 1 }))
    fixture.detectChanges()

    expect(component.tabs.length).toBe(1)
    expect(messageService.add).toHaveBeenCalled()
})
```

[comment]: # (!!!)

## Static tests

Linting

- `rake lint:backend`
- `rake lint:ui`
- `rake lint:git` (only CI)

Vulnerability checkers

- `npm audit`
- Tool for Go coming soon...
- Github Alerts

[comment]: # (!!!)

## System tests

![Stork ecosystem](media/ecosystem.png) <!-- .element: style="height:50vh; max-width:80vw; image-rendering: crisp-edges;" -->

[comment]: # (!!!)

### Architecture

![System test layers](media/layers.svg) <!-- .element: style="height:50vh; max-width:80vw; image-rendering: crisp-edges;" -->

[comment]: # (!!!)

### Overview

- Written in Python
- Require Docker
- Real interactions between Stork and Kea/BIND9
- Files in the `tests/system` directory
- Executed by `rake systemtest`

[comment]: # (!!!)

### System test - example

[comment]: # (!!!)
