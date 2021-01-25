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

### Job Scheduling

If it makes sense, create a seed scheduling the job. This is useful for jobs rescheduling themselves, providing a way to start the initial job.

``` bash
vendor/bin/phinx seed:create ScheduleObserverJob --template=vendor/neighborhoods/kojo-phinx-templates/src/Seed/ScheduleJobSeed.template.php.dist
```
Open the generated seed and update the component namespace at top of the file.
The seeder assumes the worker is named `Worker` and its actor family is generated using the [Kojo Worker Decorator Component](https://github.com/neighborhoods/KojoWorkerDecoratorComponent).

Run the generated seed whenever you want to start the job.
``` bash
vendor/bin/phinx seed:run -s ScheduleObserverJob
```
