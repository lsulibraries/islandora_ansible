
This is a complicated stack, and it requires many many instructions. These are organized to separate concerns - fundamental tasks are found in `pre-up.yml` and mysql-related tasks are controlled by a top-level play described in `mysql.yml`.

The top-level playbook for a clean build under Vagrant is `local.yml`. This, in turn, calls other high-level plays that themselves invoke roles, tasks, or other high-level plays.

For a clean build, it is important to make sure that `local.yml` is set in the Vagrantfile: `ansible.playbook = "local.yml"` in the  `config.vm.provision "ansible" do |ansible|` section. For the build to be successful, every branch in the tree starting with `local.yml` needs to be uncommented; while working on the configuration, it will be common to comment out large portions of the full build, so if you are having problems, be sure to walk the tree to where the trouble is indicated by error messages.

The build proceeds like this:

- `local.yml` - main play that starts a full build.
-- `pre-up.yml` - a meta play that does any prep work that is specific to local builds (such as setting up xdebug)
--- task: set up xdebug.ini
-- `build.yml` - heavy lifting here; kicks off major portions of the build responsible for apt packages, mysql, java/tomcat, drupal and islandora itself
--- mounts: set of tasks to attach external volumes in non-local environments
--- build-prep: install low-level reqs like php & apache using roles by geerlingguy
--- mysql: install mysql via geerlingguy role
--- databases: creates empty databases, users
--- java: installs tomcat and dependencies (ie java8) via role
--- drupal: installs a basic drupal
--- islandora: installs modules, sets app configs, for a vanilla islandora
-- `migrations.yml` - high-level play that runs an increasing number of local modifications to the basic stack (as resulting from `build.yml`)
-- `collections.yml` - for local builds, this creates sample data in the application
-- `post-up.yml` - for local builds- any cleanup that needs to happen before ansible exits

Those are just the tips of their respective icebergs, but it should be simple, if tedious, to read through the build at whatever level of detail you wish as this is just a set of linear steps, organized -ish so as to make maintainence and comprehension less difficult.