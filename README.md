# json-serialize-refs

[![NPM](https://img.shields.io/npm/v/json-serialize-refs.svg)](https://www.npmjs.com/package/json-serialize-refs) [![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com)

serialize and deserialize preserving refs ( resolving circular references automatically ) compatible with NewtonsoftJson for NET

## install

```sh
yarn add json-serialize-refs
```

## usage

```ts
/** convert given obj to json resolving references as specified by preserveType */
function stringifyRefs(obj: any, replacer: any = null, space: any = null, preserveType: PreserveType = PreserveType.All);

/** convert back from json to object reconnecting references if any */
function parseRefs(text: string, reviver?: (this: any, key: string, value: any) => any);
```

example follows:

```ts
import { stringifyRefs, parseRefs, PreserveType } from "json-serialize-refs";

interface test1_aobj_type {
    strVal: string;
}

interface test1_type {
    aobj?: test1_aobj_type;
    selftest?: test1_type;
    value?: string;
    arrdata?: (test1_type | string)[];
    selfarray?: (test1_type | string)[];
    arrdata2?: ((test1_type | string)[] | string)[];
}

const obj: test1_type = {};
obj.aobj = { strVal: "some" };
obj.selftest = obj;
obj.value = "sample string";
obj.arrdata = [obj, "other"];
obj.selfarray = obj.arrdata;
obj.arrdata2 = [obj.arrdata, "another"];

const json = stringifyRefs(obj, null, 1, PreserveType.All);
const obj2 = parseRefs(json);

console.log("selftest eq: " + String(obj2.selftest === obj2));
console.log("arrdata[0] eq: " + String(obj2.arrdata[0] === obj2));
console.log("selfarray eq: " + String(obj2.selfarray === obj2.arrdata));
console.log("arrdata2[0] eq: " + String(obj2.arrdata2[0] === obj2.arrdata));
```

## how to contribute ( quickstart )

```sh
git clone https://github.com/devel0/json-serialize-refs.git
cd json-serialize-refs
yarn install
code .
```

- hit F5 to start example

## unit tests

- run unit tests with `yarn test`
- [tests](src/test/stringify-refs.test.ts) verifies stringify and parse so that [test-data](src/example/test-data.ts) object:
    - stringified accomplish to newtonsoft json preserve reference format outputs for [Preserve All](src/test/test1-preserve-all.json) and [Preserve Objects](src/test/test1-preserve-objs.json)
    - then parsed back satisfy [object comparision](https://github.com/devel0/json-serialize-refs/blob/4201580290eff5f1b66167d1cb3f4dc494282385/src/example/test-data.ts#L16-L71)

## launchers

from vscode CTRL+SHIFT+D follows launchers available

- "Launch Program" : start example program
- "Debug Jest Tests" : run unit test with ability to breakpoint on them
- "netcore ( preserve object )" and "netcore ( preserve all )" generate newtonsoft json format into netcore folder
