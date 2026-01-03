# Dependency Injection

## Overview

Risotron uses VSCode's DI pattern:

1. Define interface with `_serviceBrand`
2. Create identifier with `createDecorator<T>()`
3. Use `@IService` decorator for injection

## Creating a Service

```typescript
import { createDecorator, Disposable, ILogService } from 'risotron';

// 1. Define interface
export interface IMyService {
  readonly _serviceBrand: undefined;
  doWork(): string;
}

// 2. Create identifier (same name as interface)
export const IMyService = createDecorator<IMyService>('myService');

// 3. Implement
export class MyService extends Disposable implements IMyService {
  declare readonly _serviceBrand: undefined;

  constructor(
    @ILogService private readonly log: ILogService
  ) {
    super();
  }

  doWork(): string {
    this.log.info('Working...');
    return 'Done';
  }
}
```

## Registering Services

### Global Registration

```typescript
import { registerSingleton, InstantiationType } from 'risotron';

// Lazy - instantiated on first access
registerSingleton(IMyService, MyService, InstantiationType.Delayed);

// Eager - instantiated immediately
registerSingleton(IMyService, MyService, InstantiationType.Eager);
```

### Instance Registration

```typescript
const app = createApplication();
app.registerService(IMyService, new MyService(logService));
```

## Using Services

### Via Application

```typescript
const myService = app.getService(IMyService);
myService.doWork();
```

### In Command Handlers

```typescript
registerCommand('my.command', (accessor) => {
  const myService = accessor.get(IMyService);
  const log = accessor.get(ILogService);
  return myService.doWork();
});
```

### Constructor Injection

```typescript
class MyFeature {
  constructor(
    @IMyService private readonly myService: IMyService,
    @ILogService private readonly log: ILogService
  ) {}

  run() {
    this.log.info('Starting');
    this.myService.doWork();
  }
}

// Create with injection
const feature = app.services.createInstance(MyFeature);
```

## Optional Dependencies

```typescript
import { optional } from 'risotron';

class MyFeature {
  constructor(
    @IRequiredService required: IRequiredService,
    @optional(IOptionalService) optional?: IOptionalService
  ) {
    if (optional) {
      // Use optional service
    }
  }
}
```

## Service Collection

For manual service setup:

```typescript
import { ServiceCollection, InstantiationService } from 'risotron';

const services = new ServiceCollection();

// Register instance
services.set(ILogService, new ConsoleLogger());

// Register factory
services.setFactory(IMyService, () => new MyService());

// Create container
const container = new InstantiationService(services);

// Resolve
const myService = container.getService(IMyService);
```

## Child Containers

For scoped services:

```typescript
const childServices = new ServiceCollection();
childServices.set(IScopedService, new ScopedServiceImpl());

const childContainer = container.createChild(childServices);
// Child can access parent services + its own
```
