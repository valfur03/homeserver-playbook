Role Name
=========

Install NodeJS and make some basic configuration.

Role Variables
--------------

You can change the NPM global directory using the variable `npm_global_directory` (`npm config set prefix "{{ npm_global_directory }}"`).


Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
        - role: nodejs
          vars:
            npm_global_directory: "{{ ansible_user_dir }}/.npm-global"

License
-------

MIT
