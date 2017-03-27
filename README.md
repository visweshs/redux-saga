
Most common 'asynchronous' APIs do infact block the main JS thread - albeit for a small amount of time. When asynchronous work scales, it becomes hard to hit the golden 60 FPS rate consistently. The only real permanent solution is web workers. 

Redux-Sagas are a fun mental model, but lack support for actual webworkers.

This library attemps to merge [promise-worker](https://github.com/nolanlawson/promise-worker) to provide
web worker support for redux-sagas.

The proposal is to leverage web workers inside generator functions, by promisifying the web worker computation.

This is how it works now:

worker.js:

```
import { registerPromiseWorker } from 'promise-worker/register';

const worker = registerPromiseWorker(function (message) {
  return Promise.resolve().then(function (message) {
    return message;
  });
});

export worker
```

saga.js

```
import { yield } from 'redux-saga'
import PromiseWorker from 'promise-worker';
const worker = new Worker('worker.js');
const promiseWorker = new PromiseWorker(worker;

function work (message) {
  promiseWorker.postMessage(message).then(function (message) {
    console.log(message): // 'much async, very promise'
  });
}

function* doWorkSaga() {
  const message = 'much async, very promise'
  const result = yield work(message)
  console.log(result) // 'much async, very promise'
}

```

This is the proposal:

saga.js

```
import { yield, {r} from 'redux-saga'
import { PromiseWorker } from 'redux-saga/effects'


const work = (message) => {
  return (message) => {
    console.log(message):
  });
}
const promiseWorker = new PromiseWorker(work);

function* doWorkSaga() {
  const message = 'much async, very promise'
  const result = yield promiseWorker(message)
  console.log(result) // 'much async, very promise'
}

```

The work done by the function ```work``` here is truly non-blocking in both examples.
