# Using as module

One of the powerful features of the `datamodel-code-generator` is the ability to use custom templates with the `--custom-template-dir` option.

This option allows you to provide a directory containing Jinja2 templates for customizing the generated code. In this document, we'll explore how to use this option and provide an example to help you understand its usage.

## Usage
To use the `--custom-template-dir` option, you'll need to pass the directory path containing your custom templates as an argument. The command will look like this:

datamodel-code-generator is a CLI. But, it has another side as a python module.

You can call this code-generator in your python code.

## How to use it as module

You can generate models with `datamodel_code_generator.generate` into a temporary directory.

And, you can read files from the directory.

### Installation
```sh
pip install "datamodel-code-generator[http]"
```

### Example
```python
from pathlib import Path
from tempfile import TemporaryDirectory
from datamodel_code_generator import InputFileType, generate

json_schema: str = """{
    "type": "object",
    "properties": {
        "number": {"type": "number"},
        "street_name": {"type": "string"},
        "street_type": {"type": "string",
                        "enum": ["Street", "Avenue", "Boulevard"]
                        }
    }
}"""

with TemporaryDirectory() as temporary_directory_name:
    temporary_directory = Path(temporary_directory_name)
    output = Path(temporary_directory / 'model.py')
    generate(
        json_schema,
        input_file_type=InputFileType.JsonSchema,
        input_filename="example.json",
        output=output,
    )
    model: str = output.read_text()
print(model)
```

The result of print(model)
```python
# generated by datamodel-codegen:
#   filename:  example.json
#   timestamp: 2020-12-21T08:01:06+00:00

from __future__ import annotations

from enum import Enum
from typing import Optional

from pydantic import BaseModel


class StreetType(Enum):
    Street = 'Street'
    Avenue = 'Avenue'
    Boulevard = 'Boulevard'


class Model(BaseModel):
    number: Optional[float] = None
    street_name: Optional[str] = None
    street_type: Optional[StreetType] = None
```

## Why doesn't the function return str?

If generated code are modular files then, str can't reproduce the structure of modules.

Also, `TemporaryDirectory` and `Path` provide an easy way to read files.

There is an example when a generated model file is only one.

Otherwise, You should walk in a directory to find modular files.


## Related projects

[fastapi-code-generator](https://github.com/koxudaxi/fastapi-code-generator) uses `datamodel-code-generator` as module to create models from OpenAPI.