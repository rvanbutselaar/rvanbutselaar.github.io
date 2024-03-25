environment variables
========================================================================

Bool:

.. code-block:: go

  // Returns the value of a boolean environment variable
  // accepts environment name as `string` and default value as a `bool`
  func getEnvBool(env string, d bool) bool {
    var v bool
    var err error

    if len(os.Getenv(env)) > 0 {
      v, err = strconv.ParseBool(os.Getenv(env))
      if err != nil {
        log.Fatalln(err.Error())
      }
    } else {
      v = d
    }
    return v
  }

  dryrun := getEnvBool("DRYRUN", true)


String:

.. code-block:: go

  // Returns the value of a string environment variable
  // accepts environment name as `string` and default value as a `string`
  func getEnvString(env string, d string) string {
    var v string

    if len(os.Getenv(env)) > 0 {
      v = os.Getenv(env)
    } else {
      v = d
    }
    return v
  }

  ns := getEnvString("NAMESPACE", "openshift-logging")