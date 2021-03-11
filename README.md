# Phinx templates for Kōjō

A developer utility to generate [Kōjō](https://github.com/neighborhoods/Kojo) related [Phinx](https://github.com/cakephp/phinx) migrations and seeds.

## Install

Via Composer as a development dependency

``` bash
$ composer require --dev neighborhoods/kojo-phinx-templates
```

You should also have [Phinx](https://github.com/cakephp/phinx) and [Kōjō](https://github.com/neighborhoods/Kojo) as direct dependencies. Don't rely on this package pulling them in, especially since this is a dev dependency.

This package requires Phinx 0.12.5 or above. If you're having issues with running the command above, send the `composer.json` file to the code owner.

## Use cases

Possible use cases are described below.

### Kōjō setup

Create migration for Kōjō setup. Every project using Kōjō should have it.

``` bash
vendor/bin/phinx create InstallKojo --template=vendor/neighborhoods/kojo-phinx-templates/src/Migration/InstallKojoMigration.template.php.dist
```

If you open the file you'll see that the template assumes that Kōjō parameters are located in the `kojo-environment` folder inside the current working directly.  
That's because the `kojo-environment` folder is usually located in the application root from where migrations are usually run using `vendor/bin/phinx migrate` command.  
You might need to update the path to Kōjō parameters. If so, consider defining it relative to the migration file, using the `__DIR__` constant.

### Job Type Creation

For each new worker generate a migration defining the job type. In these examples the worker name is Observer. Use something more descriptive, like the component name.

``` bash
vendor/bin/phinx create CreateObserverJobType --template=vendor/neighborhoods/kojo-phinx-templates/src/Migration/CreateJobTypeMigration.template.php.dist
```

Open the generated migration, update the job type attributes. The values in the migration should not depend on other classes. Copy constant values, rather than referencing them.

Thanks to @jpmarcotte for insights on this one.

### Job Scheduling

If it makes sense, create a seeder scheduling the job. This is useful for jobs rescheduling themselves, providing a way to start the initial job.

``` bash
vendor/bin/phinx seed:create ObserverJobSeeder --template=vendor/neighborhoods/kojo-phinx-templates/src/Seed/JobSeeder.template.php.dist
```
Open the generated seeder and update the `JOB_TYPE_CODE` constant.

Run the generated seeder whenever you want to start the job.
``` bash
vendor/bin/phinx seed:run -s ObserverJobSeeder
```

Rescheduling jobs should be seeded after their job type creation. Integrating the seeding into migrations simplifies the deployment process and ensures the job is seeded only once.  
Generate a seeding migration by running the command below.
``` bash
vendor/bin/phinx create SeedObserverJob --template=vendor/neighborhoods/kojo-phinx-templates/src/Migration/SeedJobMigration.template.php.dist
```
Open the generated migration and update the Seed class name in the `up()` and `down()` methods.
