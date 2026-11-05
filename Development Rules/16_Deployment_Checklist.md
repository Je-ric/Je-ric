# Deployment Checklist

Use for an authorized deployment, not as commands to execute after every edit.

- Verify the target environment and reviewed release; run relevant checks before deployment.
- Review migrations for data loss, locking, existing duplicates, and compatibility. Confirm a recoverable backup and rollback/recovery plan before production schema changes.
- Install dependencies from lockfiles and build production assets using the established deployment process.
- Verify production configuration without printing secrets: environment, debug disabled, URL, database, mail, storage, queue, and session settings.
- Run `php artisan migrate --force` only as part of the authorized deployment. Never use `migrate:fresh` against live data.
- Create the storage link only if required and absent. Rebuild configuration/route/view caches according to the deployment setup; do not blindly clear application data caches.
- Restart long-running workers when needed and verify scheduler/queue operation.
- Smoke-test login, a protected route, assets, and the changed workflow. Inspect logs for new errors and follow the recovery plan if checks fail.

See `14_Debugging_and_Troubleshooting.md`, `14_Debugging_and_Troubleshooting.md`, and `01_Agent_Guidelines_and_Workflow.md`.
