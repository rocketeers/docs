### Rocketeer\TasksQueue

	add($task)

Register a custom Task with Rocketeer

	before($task, $surroundingTask)

Execute a Task before another one

	after($task, $surroundingTask)

Execute a Task after another one

	getBefore(Task $task)

Get the tasks to execute before a Task

	getAfter(Task $task)

Get the tasks to execute after a Task

	run(array $tasks, $command = null)

Run a provided queue, pass the `$command` to built Tasks

	buildQueue(array $tasks)

Build a queue from a list of tasks

	buildTaskFromClosure($task)

Build a Task from a Closure or a string command

	buildTask($task)

Build a Task from its name