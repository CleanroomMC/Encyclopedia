# Getting Started

## Adding ModularUI to your mod
Add the maven to the repositories (if not already present)
````groovy
maven {
    name 'CurseMaven'
    url 'https://cursemaven.com'
    content {
        includeGroup 'curse.maven'
    }
}
````
and the dependency declaration to dependencies
````groovy
implementation 'curse.maven:modularui-624243:4786372-sources-4786373'
````

- `624243` is the Curseforge project id of ModularUI
- `4779301` is the `dev` file id (for version 2.2.3)
- `4779302` is the `sources` file id (for version 2.2.3)

Make sure to use the latest version!