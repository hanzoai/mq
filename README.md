<div align="center">
  <br/>
  <img src="https://hanzo.ai/logo.png" width="200" />
  <br/>
  <br/>
  <h1>@hanzo/mq</h1>
  <p>
    The fastest, most reliable, distributed queue for Node.js. <br/>
    Built on Hanzo KV (Valkey/Redis compatible). Carefully written for rock solid stability and atomicity.
  </p>
  <p>
    <a href="https://hanzo.ai">
      <img src="https://img.shields.io/badge/Hanzo_AI-4A154B"/>
    </a>
    <a href="https://www.npmjs.com/package/@hanzo/mq">
      <img src="https://img.shields.io/npm/v/@hanzo/mq"/>
    </a>
    <a href="https://github.com/hanzoai/mq">
      <img src="https://img.shields.io/badge/GitHub-hanzoai%2Fmq-blue"/>
    </a>
  </p>
</div>

# Overview

@hanzo/mq is a message queue and job processing library for Node.js, built to work with Hanzo KV, Valkey, and Redis. It's part of the [Hanzo AI](https://hanzo.ai) infrastructure ecosystem.

This package is based on [BullMQ](https://github.com/taskforcesh/bullmq) and maintains full API compatibility.

## Installation

```shell
npm install @hanzo/mq
```

## Quick Start

Add jobs to the queue:

```ts
import { Queue } from '@hanzo/mq';

const queue = new Queue('Paint');

queue.add('cars', { color: 'blue' });
```

Process jobs with workers:

```ts
import { Worker } from '@hanzo/mq';

const worker = new Worker('Paint', async job => {
  if (job.name === 'cars') {
    await paintCar(job.data.color);
  }
});
```

Listen for job completion:

```ts
import { QueueEvents } from '@hanzo/mq';

const queueEvents = new QueueEvents('Paint');

queueEvents.on('completed', ({ jobId }) => {
  console.log('done painting');
});

queueEvents.on(
  'failed',
  ({ jobId, failedReason }: { jobId: string; failedReason: string }) => {
    console.error('error painting', failedReason);
  },
);
```

## Parent-Child Job Dependencies

```ts
import { FlowProducer } from '@hanzo/mq';

const flow = new FlowProducer();

const originalTree = await flow.add({
  name: 'root-job',
  queueName: 'topQueueName',
  data: {},
  children: [
    {
      name: 'child-job',
      data: { idx: 0, foo: 'bar' },
      queueName: 'childrenQueueName',
      children: [
        {
          name: 'grandchild-job',
          data: { idx: 1, foo: 'bah' },
          queueName: 'grandChildrenQueueName',
        },
        {
          name: 'grandchild-job',
          data: { idx: 2, foo: 'baz' },
          queueName: 'grandChildrenQueueName',
        },
      ],
    },
    {
      name: 'child-job',
      data: { idx: 3, foo: 'foo' },
      queueName: 'childrenQueueName',
    },
  ],
});
```

## Features

| Feature                    | @hanzo/mq |
| :------------------------- | :-------: |
| Backend                    | Hanzo KV / Valkey / Redis |
| Parent/Child Dependencies  | ✓ |
| Deduplication (Debouncing) | ✓ |
| Deduplication (Throttling) | ✓ |
| Priorities                 | ✓ |
| Concurrency                | ✓ |
| Delayed jobs               | ✓ |
| Global events              | ✓ |
| Rate Limiter               | ✓ |
| Pause/Resume               | ✓ |
| Sandboxed worker           | ✓ |
| Repeatable jobs            | ✓ |
| Atomic ops                 | ✓ |
| Persistence                | ✓ |
| UI                         | ✓ |

## Connection Options

@hanzo/mq connects to Hanzo KV, Valkey, or Redis:

```ts
import { Queue } from '@hanzo/mq';

// Default connection (localhost:6379)
const queue = new Queue('myqueue');

// Custom connection
const queue = new Queue('myqueue', {
  connection: {
    host: 'hanzo-kv.example.com',
    port: 6379,
    password: 'your-password',
  },
});
```

## Hanzo Ecosystem

@hanzo/mq is part of the Hanzo AI infrastructure:

- **[Hanzo KV](https://github.com/hanzoai/kv)** - High-performance key-value server (Valkey fork)
- **[@hanzo/kv-client](https://github.com/hanzoai/kv-client)** - Node.js client for Hanzo KV
- **[Hanzo Auto](https://github.com/hanzoai/auto)** - Workflow automation platform
- **[Hanzo Flow](https://github.com/hanzoai/flow)** - Visual AI/LLM workflow builder

## Documentation

For detailed documentation, see the [BullMQ documentation](https://docs.bullmq.io) as @hanzo/mq maintains API compatibility.

## Contributing

Fork the repo, make some changes, submit a pull-request!

## License

MIT

## Credits

This package is based on [BullMQ](https://github.com/taskforcesh/bullmq) by Taskforce.sh.
