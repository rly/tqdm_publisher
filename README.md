# tqdm-publisher
[![PyPI version](https://badge.fury.io/py/tqdm-publisher.svg)](https://badge.fury.io/py/tqdm-publisher.svg)
[![License](https://img.shields.io/pypi/l/tqdm-publisher.svg)](https://github.com/catalystneuro/tqdm-publisher/license.txt)

`tqdm-publisher` is a small Python package that allows you to subscribe to updates from `tqdm` progress bars with arbitrary callback functions.

This is useful if you want to use `tqdm` to track the progress of a long-running task, but you also want to do something else with the progress information (e.g., forward it to a user interface, log it to a file, etc.).

## Installation
```bash
pip install tqdm-publisher
```

## Usage
```python
import random
import asyncio

from tqdm_publisher import TQDMPublisher

async def sleep_func(sleep_duration = 1):
    await asyncio.sleep(delay=sleep_duration)

async def run_multiple_sleeps(sleep_durations):
    
    tasks = []

    for sleep_duration in sleep_durations:
        task = asyncio.create_task(sleep_func(sleep_duration=sleep_duration))
        tasks.append(task)

    progress_bar = TQDMPublisher(asyncio.as_completed(tasks), total=len(tasks))
    callback_id = progress_bar.subscribe(lambda info: print('Progress Update', info))

    for f in progress_bar:
        await f

    progress_bar.unsubscribe(callback_id)

n = 10**5
sleep_durations = [random.uniform(0, 5.0) for _ in range(n)]
asyncio.run(run_multiple_sleeps(sleep_durations=sleep_durations))
```