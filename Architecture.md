# API

Rocketeer is composed the following way :

| Namespace        | Class               | Description                                                                                     |
| ---------------- | ---------------     | ----------------------------------------------------------------------------------------------- |
| Rocketeer        | [Bash][]            | An helper to execute low-level commands on the remote server                                    |
| Rocketeer        | [ReleasesManager][] | Provides informations and actions around releases                                               |
| Rocketeer        | [Rocketeer][]       | Handles interaction between the User provided informations and the various Rocketeer components |
| Rocketeer        | [Server][]          | Provides and persists informations about the remote server                                      |
| Rocketeer        | [TasksQueue][]      | Handles the registering of Tasks and their execution                                            |
| Rocketeer\Scm    | Git                 | The Git SCM                                                                                     |
| Rocketeer\Tasks  | [Check][]           | Check if the server is ready to receive the application                                         |
| Rocketeer\Tasks  | Cleanup             | Clean up old releases from the server                                                           |
| Rocketeer\Tasks  | Closure             | A task formatted as a Closure                                                                   |
| Rocketeer\Tasks  | CurrentRelease      | Display what the current release is                                                             |
| Rocketeer\Tasks  | [Deploy][]          | Deploy the website                                                                              |
| Rocketeer\Tasks  | Rollback            | Rollback to the previous release, or to a specific one                                          |
| Rocketeer\Tasks  | Setup               | Set up the remote server for deployment                                                         |
| Rocketeer\Tasks  | Teardown            | Remove the remote applications and existing caches                                              |
| Rocketeer\Tasks  | Test                | Run the tests on the server and displays the ouput                                              |
| Rocketeer\Tasks  | Update              | Update the remote server without doing a new release                                            |
| Rocketeer\Traits | Scm                 | A base trait for SCM classes                                                                    |
| Rocketeer\Traits | [Task][]            | An abstract Task with common helpers, from which all Tasks derive                               |

  [Deploy]: https://github.com/Anahkiasen/rocketeer/wiki/Rocketeer-Tasks-Deploy
  [Bash]: https://github.com/Anahkiasen/rocketeer/wiki/Rocketeer-Bash
  [Check]: https://github.com/Anahkiasen/rocketeer/wiki/Rocketeer-Tasks-Check
  [ReleasesManager]: https://github.com/Anahkiasen/rocketeer/wiki/Rocketeer-ReleasesManager
  [Rocketeer]: https://github.com/Anahkiasen/rocketeer/wiki/Rocketeer-Rocketeer
  [Server]: https://github.com/Anahkiasen/rocketeer/wiki/Rocketeer-Server
  [Task]: https://github.com/Anahkiasen/rocketeer/wiki/Rocketeer-Traits-Task
  [TasksQueue]: https://github.com/Anahkiasen/rocketeer/wiki/Rocketeer-TasksQueue