# Modernizing Legacy PHP Apps

## Incremental approach
- Pay off smallest debt
- Small changes across the codebase
- Build on previous small changes
- Improve quality over time

### Incremental goals
Keep the application running
Consolidate classes for autoloading (PSR-0)
Incidentally, also functions
That way we can remove all the includes and requires everywhere
Convert globals to injected dependencies
After each change (one small change), “spot check”, commit, push, QA
“Spot check” tests the output of the system. Probably not a unit test, because most likely you don’t have them.
ie, get the output of the page, make the change, get the output of the page. If it’s the same, good.

### Autoloading
PSR-0 autoloading
Class name maps directly to file name
Namespace separators map directly to directory separators
Class underscores map to directory separators
Need all our classes in one single directory
So we write the autoloader, first step
Then we search code for classes, and move them to the central directory
For every class
Move it to the new folder
Search for includes for that file, and remove it
That’s our one step. Spot check it, commit, push, send off to QA
Convert function files to class files
They can’t be autoloaded as functions
To convert them, wrap them in classes and either make them static or instance methods
Move these function files to classes directory
Change the calls to these functions using either static syntax or instance
Remove the include and require statements as you go
Can at same time rename functions to be PSR compliant also
Convert globals to injected dependencies
Move the global variable (eg, $db) to a class constructor (create one if it doesn’t exist)
That’s one change. Spot check, commit, push, QA
Second step is to remove the global from the constructor and inject it into it via a parameter. Set $this->db = $db for example
Then search the code for the new class instantiation and create the variable there, and pass it in when you instantiate. Spot check, commit, push, QA
Do this with each class until all globals are removed
Problem: some dependencies have dependencies
Ask yourself if the calling class needs the global you need to pass in? It may not.
So, pass in the dependent class as a dependency injection to the class constructor
So you end up creating the $db, injecting it into the class that needs it, then inject that class into the other class

### Initial goals completed
Consolidated into classes with PSR-0 and autoloading
Removed globals in favor of dependency injection 
Kept it running the whole time
Paid off some technical debt
Set up an organizational structure for future work
We can start writing unit tests

### But wait! There’s more!
Using new keyword (remove it everywhere from the code)
Separation of concerns:
Object creation (factories to create classes)
Object use (created classes perform their logic)
Embedded SQL statements
Consolidate it into gateway classes
Then our SQL code is independently testable
We can even swap it out with a DBAL
Embedded domain logic
Consolidate it into transaction scripts
Also independently testable, even from the SQL
Embedded presentation logic
All the response work, presentation, headers, etc
Embedded action logic
Put this into either controller or action classes
Embedded include calls
Put the few of these that are left into their own classes and methods
Router + front controller
These can then point to our new controllers to de-couple the page scripts
DI container
