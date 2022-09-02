# Migration to Drupal

> A basic of tips, best practices and resources about migration to Drupal.

## Prepare for migration

Most of the times you need to create a report about the site/source that will be migrated to Drupal.

If you are going to migrate from an old (7.x or less) Drupal site and need to generate a Report please check [drupal-report](https://github.com/theodorosploumis/drupal-report).

## Tips

- Generate a sheet of the entities, fields etc to be migrated.
- Use always the **migrate_plus** module.
- Always create and enable a custom module that will contain any **custom migration plugins** and initial **configuration**.
- Migrate only data, not settings or other special configuration (eg avoid migrating permissions, site settings, node settings etc).
- Don't rely on existing core (or contrib) yml files. Always clone and override these to your custom module to be able to alter it later.
- Some core yml files use many other migrations as dependencies. Keep things simple and create a custom migration Plugin instead of using many migrations.
- Every migration is unique and not generic and refers to a specific website but prefer a generic custom module name to allow easy copy - paste of files for future migrations.
- Create Groups of migrations that are supposed to run together (eg Paragraphs) and create the same entities on the new website.
- Always try to run migrations from cli (drush) and not the UI.
- Create several Views on the old website (that will be migrated) if it is built on Drupal that allows you to get an overview of the old data and have a UI when testing (Nodes, Users, Paragraphs, Fields, Menu links etc).
- Document the custom module with a README file (mappings, process, drush commands, what to excpect, dependencies, manual process, database credentials example etc).
- While developing the migrations import and export configuration continuously until you get the results you expect. When the experiments are finished copy the final yml files back to the custom module (after removing key `_core`).
- Before migrating private files it is required to copy them to the new website (on the private folder) using rsync or any other similar tool.
- If there is a multilingual node migration to happen use a custom field to keep information about the old nid (eg an integer field) to allow you better testing.
- Be careful when migrating Drupal 7.x (or 6.x) websites that have asymmetric field values (eg Paragraphs).
- If you want to skip a migration row you have to `return FALSE;` on the `function prepareRow(Row $row)`.
- If you want to get real distinct rows (eg only 1 row with the same title) do not try to do this on the `query()` function but on the `prepareRow(Row $row)` function. Function `query()` usually returns error when running the migration due to the mysql restrictions.
- If you need to get data from html tags or massage html content use the PHP `DOMDocument` library.
- When you create demo content on Stage Environment and then run migrations again with update you may have unsxpected results because the new migrated ids may exist and will be skipped.
- After migration is run on the Production website uninstall all the migration related modules.
- Search on [Drupal.org code](https://git.drupalcode.org/search?search=migration_dependencies&project_id=59858&group_id=2) for migration yml files before creating your custom one.
- Search on Drupal.org for contributed modules related to a special migration (eg Media) before creating your custom one.

## Testing migrations

- Random test some old `/node/NID` paths exist on the new website.
- For a multilingual website an old node that is not the translation source should have a new nid.
- Test an old node of type X that references some Paragraphs of type Y do have the same Paragraphs on the new website.
- Test path aliases exist.
- Test menu links exist.
- Test files exist.
- Test field values of Paragraph X exist (this needs a database lookup or a custom Views to inspect).
- Test language of the website (redirect) when visiting as anonymous user (check if redirect follows the user browser).
- Test node translation form: add
- Test node translation form: edit
- Test node translation form: delete
- Test node translation form: unpublish (should hide the equivalent menu link and language switch link)
- Test node: add translation

## References

Notice: The references were added for the 9.0.x version.

- [Drupal.org Docs - Migrate API](https://www.drupal.org/docs/drupal-apis/migrate-api)
- [drupalmigrate.org](https://drupalmigrate.org)
- [migrate_tools drush commands](https://www.drupal.org/node/1561820)
- [Drupal core migrate yml files, 1](https://git.drupalcode.org/project/drupal/-/tree/9.0.x/core/modules/node/src/Plugin/migrate/source)
- [Drupal core migrate yml files, 2](https://git.drupalcode.org/project/drupal/-/tree/9.0.x/core/modules/node/migrations)
- [Migrate Drupal from 8.x to 10.x](migration-8-plus)

## Useful modules to install

- migrate_tools
- migrate_plus
- migrate_devel

## DB example for migration

```php
// This is an example of a migration database settings.
// The key "migrate" is the default.
$databases['migrate']['default'] = array (
  'database' => 'drupal7',
  'username' => 'drupal7',
  'password' => 'drupal7',
  'prefix' => '',
  'host' => 'database.7x-website.host',
  'port' => '3306',
  'namespace' => 'Drupal\\Core\\Database\\Driver\\mysql',
  'driver' => 'mysql',
);
```
