# lein-resource

A plugin that can be used to copy files from mulitple source
directories to a target directory while maintaining the subdirecotries.  Also, each file
will be transformed using [stencil](https://github.com/davidsantiago/stencil).  The map 
that is passed to stencil contains a combination of:

* The project map
* The system properties (with .prop added to the name )
* Additional values (currently only :timestamp)
* Values set in the project.clj using :resource :extra-values

## Usage

To use from Leiningen add to `project.clj`:

      :plugins [ [lein-resource "0.3.0"] ]

To have it run before the jar file creation:

      :prep-tasks ["javac" "compile" "resource"]

To have it run before compile and after clean:

      :hooks [leiningen.resource]

To configure lein-resource, add to `project.clj`

    :resource {
        :resource-paths ["src-resource"] ;; required or does nothing
        :target-path "target/html"      ;; optional default to the global one
		:includes [ #".*" ]   ;;  optional - this is the default
		:excludes [ #".*~" ]   ;;  optional - default is no excludes which is en empty vector
        :extra-values { :year ~(.get (java.util.GregorianCalendar.)
                                         (java.util.Calendar/YEAR)) }  ;; optional - default to nil

If `:resource-paths` is not set or is nil, then it won't do anything

To see all the properties that are passed to stencil:

    lein resource pprint

To delete all the copied files and empty directories:

    lein resource clean

Note that stencil/mustache uses dot notation (.) for nested maps.  For example, to get the username 
system property use:

    {{user.name.prop}}

A file that uses this plugin would contain:

    #
    # {{description}}
    # Build date: {{timestamp}}
    # Built by: {{user.name.prop}}
    # Copyright {{year}}
    #

* description is pulled from the project.clj
* timestamp is pulled from the addition fields
* user.name.prop is pulled from the system properties.  The system properties always have .prop on the end
* year is pulled from the `exta-values` defined in the project.clj

To see it in action, see the [Tic Tac Toe project.clj](https://github.com/m0smith/tic-tac-toe/blob/master/project.clj)


## License

Copyright &copy; 2012 Matther O. Smith

Distributed under the Eclipse Public License, the same as Clojure.
