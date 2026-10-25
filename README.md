# NestJS + BullMQ Practice — Rohit Chigatapu

Hands-on practice with [BullMQ](https://docs.bullmq.io/) job queues in NestJS, using a mock "video processing" service as the example.

**Repo:** https://github.com/Rohit04052004/nestjs-bullmq-practice

## What's practiced here

- **Registering a queue** — `BullModule.forRoot()` with Redis connection config and default job options (`attempts`, `backoff`, `removeOnComplete`/`removeOnFail`), plus `BullModule.registerQueue({ name: 'video' })`
- **Producing jobs** — a controller (`VideoController`) that adds named jobs (`process`, `compress`) to the queue via `@InjectQueue`, including a delayed job (`{ delay: 5000 }`)
- **Consuming jobs with a worker** — `VideoProcessor` (`@Processor('video', { concurrency: 3 })`) that handles different job names and runs jobs concurrently
- **Job progress tracking** — simulating multi-step work and reporting progress with `job.updateProgress()`
- **Worker lifecycle events** — `@OnWorkerEvent` for `active`, `progress`, `completed`, and `failed`, including reading `job.attemptsMade` on failure
- **Queue-level events** — a separate `@QueueEventsListener` (`VideoQueueEventsListener`) listening for queue events like `added`, independent of the worker

## Tech stack

NestJS, BullMQ, Redis, TypeScript.

## Running locally

Requires a local Redis server on `localhost:6379` (the connection BullMQ is configured to use in `src/app.module.ts`).

```bash
npm install
npm run start:dev
```

Then trigger jobs:

```bash
curl -X POST http://localhost:3000/video/process
curl -X POST http://localhost:3000/video/compress
```

Watch the console for queue/worker log output (job added → active → progress → completed).

## Credits

Adapted from an open-source NestJS + BullMQ example project, worked through and maintained here by Rohit Chigatapu.
