Sagas are cool, but lack support for webworkers.

This library attemps to merge promise-worker[https://github.com/nolanlawson/promise-worker] to provide
web worker support for redux-sagas.

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
  return (message =>) {
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
