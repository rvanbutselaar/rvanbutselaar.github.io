Init cobra cli
========================================================================

Init cobra inside the application / git project folder:

.. code-block:: bash

  go mod init harbor-cli
  ~/go/bin/cobra init --pkg-name harbor-cli
  go get github.com/spf13/cobra github.com/spf13/viper

To add a new command:

.. code-block:: bash

  ~/go/bin/cobra add ping