```
  Here are the standard levels with their default severity value, each one is associated with
        a logging method of the same name:

        +----------------------+------------------------+------------------------+
        | Level name           | Severity value         | Logger method          |
        +======================+========================+========================+
        | ``TRACE``            | 5                      | |logger.trace|         |
        +----------------------+------------------------+------------------------+
        | ``DEBUG``            | 10                     | |logger.debug|         |
        +----------------------+------------------------+------------------------+
        | ``INFO``             | 20                     | |logger.info|          |
        +----------------------+------------------------+------------------------+
        | ``SUCCESS``          | 25                     | |logger.success|       |
        +----------------------+------------------------+------------------------+
        | ``WARNING``          | 30                     | |logger.warning|       |
        +----------------------+------------------------+------------------------+
        | ``ERROR``            | 40                     | |logger.error|         |
        +----------------------+------------------------+------------------------+
        | ``CRITICAL``         | 50                     | |logger.critical|      |
        +----------------------+------------------------+------------------------+

        .. _record:

        .. rubric:: The record dict

        The record is just a Python dict, accessible from sinks by ``message.record``. It contains
        all contextual information of the logging call (time, function, file, line, level, etc.).

        Each of its key can be used in the handler's ``format`` so the corresponding value is
        properly displayed in the logged message (e.g. ``"{level}"`` -> ``"INFO"``). Some record's
        values are objects with two or more attributes, these can be formatted with ``"{key.attr}"``
        (``"{key}"`` would display one by default). `Formatting directives`_ like ``"{key: >3}"``
        also works and is particularly useful for time (see below).

        +------------+---------------------------------+----------------------------+
        | Key        | Description                     | Attributes                 |
        +============+=================================+============================+
        | elapsed    | The time elapsed since the      | See |timedelta|            |
        |            | start of the program            |                            |
        +------------+---------------------------------+----------------------------+
        | exception  | The formatted exception if any, | ``type``, ``value``,       |
        |            | ``None`` otherwise              | ``traceback``              |
        +------------+---------------------------------+----------------------------+
        | extra      | The dict of attributes          | None                       |
        |            | bound by the user (see |bind|)  |                            |
        +------------+---------------------------------+----------------------------+
        | file       | The file where the logging call | ``name`` (default),        |
        |            | was made                        | ``path``                   |
        +------------+---------------------------------+----------------------------+
        | function   | The function from which the     | None                       |
        |            | logging call was made           |                            |
        +------------+---------------------------------+----------------------------+
        | level      | The severity used to log the    | ``name`` (default),        |
        |            | message                         | ``no``, ``icon``           |
        +------------+---------------------------------+----------------------------+
        | line       | The line number in the source   | None                       |
        |            | code                            |                            |
        +------------+---------------------------------+----------------------------+
        | message    | The logged message (not yet     | None                       |
        |            | formatted)                      |                            |
        +------------+---------------------------------+----------------------------+
        | module     | The module where the logging    | None                       |
        |            | call was made                   |                            |
        +------------+---------------------------------+----------------------------+
        | name       | The ``__name__`` where the      | None                       |
        |            | logging call was made           |                            |
        +------------+---------------------------------+----------------------------+
        | process    | The process in which the        | ``name``, ``id`` (default) |
        |            | logging call was made           |                            |
        +------------+---------------------------------+----------------------------+
        | thread     | The thread in which the         | ``name``, ``id`` (default) |
        |            | logging call was made           |                            |
        +------------+---------------------------------+----------------------------+
        | time       | The aware local time when the   | See |datetime|             |
        |            | logging call was made           |                            |
        +------------+---------------------------------+----------------------------+

        .. _time:

        .. rubric:: The time formatting

        To use your favorite time representation, you can set it directly in the time formatter
        specifier of your handler format, like for example ``format="{time:HH:mm:ss} {message}"``.
        Note that this datetime represents your local time, and it is also made timezone-aware,
        so you can display the UTC offset to avoid ambiguities.

        The time field can be formatted using more human-friendly tokens. These constitute a subset
        of the one used by the `Pendulum`_ library of `@sdispater`_. To escape a token, just add
        square brackets around it, for example ``"[YY]"`` would display literally ``"YY"``.

        If you prefer to display UTC rather than local time, you can add ``"!UTC"`` at the very end
        of the time format, like ``{time:HH:mm:ss!UTC}``. Doing so will convert the ``datetime``
        to UTC before formatting.

        If no time formatter specifier is used, like for example if ``format="{time} {message}"``,
        the default one will use ISO 8601.

        +------------------------+---------+----------------------------------------+
        |                        | Token   | Output                                 |
        +========================+=========+========================================+
        | Year                   | YYYY    | 2000, 2001, 2002 ... 2012, 2013        |
        |                        +---------+----------------------------------------+
        |                        | YY      | 00, 01, 02 ... 12, 13                  |
        +------------------------+---------+----------------------------------------+
        | Quarter                | Q       | 1 2 3 4                                |
        +------------------------+---------+----------------------------------------+
        | Month                  | MMMM    | January, February, March ...           |
        |                        +---------+----------------------------------------+
        |                        | MMM     | Jan, Feb, Mar ...                      |
        |                        +---------+----------------------------------------+
        |                        | MM      | 01, 02, 03 ... 11, 12                  |
        |                        +---------+----------------------------------------+
        |                        | M       | 1, 2, 3 ... 11, 12                     |
        +------------------------+---------+----------------------------------------+
        | Day of Year            | DDDD    | 001, 002, 003 ... 364, 365             |
        |                        +---------+----------------------------------------+
        |                        | DDD     | 1, 2, 3 ... 364, 365                   |
        +------------------------+---------+----------------------------------------+
        | Day of Month           | DD      | 01, 02, 03 ... 30, 31                  |
        |                        +---------+----------------------------------------+
        |                        | D       | 1, 2, 3 ... 30, 31                     |
        +------------------------+---------+----------------------------------------+
        | Day of Week            | dddd    | Monday, Tuesday, Wednesday ...         |
        |                        +---------+----------------------------------------+
        |                        | ddd     | Mon, Tue, Wed ...                      |
        |                        +---------+----------------------------------------+
        |                        | d       | 0, 1, 2 ... 6                          |
        +------------------------+---------+----------------------------------------+
        | Days of ISO Week       | E       | 1, 2, 3 ... 7                          |
        +------------------------+---------+----------------------------------------+
        | Hour                   | HH      | 00, 01, 02 ... 23, 24                  |
        |                        +---------+----------------------------------------+
        |                        | H       | 0, 1, 2 ... 23, 24                     |
        |                        +---------+----------------------------------------+
        |                        | hh      | 01, 02, 03 ... 11, 12                  |
        |                        +---------+----------------------------------------+
        |                        | h       | 1, 2, 3 ... 11, 12                     |
        +------------------------+---------+----------------------------------------+
        | Minute                 | mm      | 00, 01, 02 ... 58, 59                  |
        |                        +---------+----------------------------------------+
        |                        | m       | 0, 1, 2 ... 58, 59                     |
        +------------------------+---------+----------------------------------------+
        | Second                 | ss      | 00, 01, 02 ... 58, 59                  |
        |                        +---------+----------------------------------------+
        |                        | s       | 0, 1, 2 ... 58, 59                     |
        +------------------------+---------+----------------------------------------+
        | Fractional Second      | S       | 0 1 ... 8 9                            |
        |                        +---------+----------------------------------------+
        |                        | SS      | 00, 01, 02 ... 98, 99                  |
        |                        +---------+----------------------------------------+
        |                        | SSS     | 000 001 ... 998 999                    |
        |                        +---------+----------------------------------------+
        |                        | SSSS... | 000[0..] 001[0..] ... 998[0..] 999[0..]|
        |                        +---------+----------------------------------------+
        |                        | SSSSSS  | 000000 000001 ... 999998 999999        |
        +------------------------+---------+----------------------------------------+
        | AM / PM                | A       | AM, PM                                 |
        +------------------------+---------+----------------------------------------+
        | Timezone               | Z       | -07:00, -06:00 ... +06:00, +07:00      |
        |                        +---------+----------------------------------------+
        |                        | ZZ      | -0700, -0600 ... +0600, +0700          |
        |                        +---------+----------------------------------------+
        |                        | zz      | EST CST ... MST PST                    |
        +------------------------+---------+----------------------------------------+
        | Seconds timestamp      | X       | 1381685817, 1234567890.123             |
        +------------------------+---------+----------------------------------------+
        | Microseconds timestamp | x       | 1234567890123                          |
        +------------------------+---------+----------------------------------------
```



