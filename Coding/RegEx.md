- Requires valid email address:

- /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/

- Requires valid US phone number:

- /^(\+\d{1,2}\s?)?\(?\d{3}\)?[\s.-]?\d{3}[\s.-]?\d{4}$/

- Requires a valid DOB in MM/DD/YYYY format:

- /^(0[1-9]|1[0-2])\/(0[1-9]|[12][0-9]|3[01])\/\d{4}$/

- Requires valid phone number:

- /^(\+|\(|\d)+( |\(|\d|-)+\d+.*\d+.*/

- Vanderbilt hyperspace upload MRN fixup, regex only matches last 8 characters

- /(?<=\d)\d{8}/

- How to match if it is empty:

- /^((?!.+).)*$/

- How not to match a string and match blank/null:

- /^((?!None).)*$/

- How not to match a string or match blank/null:

- /^((?!None).)+$/

- How to force HH:MM format with 0-23 for HH and 0-59 MM

- /^([01]\d|2[0-3]):([0-5]\d)$/

- Will match if string has 17 or more characters

- /^.{17,}/

- Matches anything before the last instance of an underscore

- /.*(?=_[^_]*$)/

- Matches the first 2-5 characters

- /^.{2,5}/

- Matches what is before the 9 ^

- /^(.*?)\^{9}/

- {$PV1_8 =~ /^(.*?)\^{9}/; return $1}

- Used to test: [Regex 101](https://regex101.com/)