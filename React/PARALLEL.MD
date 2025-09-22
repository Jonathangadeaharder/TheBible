# React Parallel Programming Guidelines

React applications can leverage parallelism through Web Workers, concurrent rendering, and asynchronous patterns.

## Concurrent Rendering (React 18+)

### Automatic Batching
```javascript
// React 18 batches multiple state updates automatically
function handleClick() {
    setCount(c => c + 1);
    setFlag(f => !f);
    // React will re-render once at the end (batched)
}
```

### Transitions for Non-Urgent Updates
```javascript
import { startTransition } from 'react';

function TabContainer() {
    const [isPending, startTransition] = useTransition();

    function selectTab(tabId) {
        startTransition(() => {
            setTabId(tabId); // Non-urgent update
        });
    }

    return (
        <div>
            {isPending && <Spinner />}
            <Tabs onTabSelect={selectTab} />
        </div>
    );
}
```

### Suspense for Data Fetching
```javascript
// Components can suspend while loading data
<Suspense fallback={<Spinner />}>
    <UserProfile userId={userId} />
</Suspense>

// Concurrent rendering enables smooth transitions
<Profiler id="navigation" onRender={onRenderCallback}>
    <Suspense fallback={<PageSkeleton />}>
        <MainContent />
    </Suspense>
</Profiler>
```

## Web Workers for CPU-Intensive Tasks

### Worker Creation
```javascript
// heavy-computation-worker.js
self.onmessage = function(e) {
    const { data } = e;
    const result = performHeavyComputation(data);
    self.postMessage({ result });
};

// In React component
const [result, setResult] = useState(null);
const [loading, setLoading] = useState(false);

useEffect(() => {
    const worker = new Worker('/heavy-computation-worker.js');

    worker.onmessage = function(e) {
        setResult(e.data.result);
        setLoading(false);
    };

    return () => worker.terminate();
}, []);

const startComputation = (data) => {
    setLoading(true);
    worker.postMessage({ data });
};
```

### Comlink for Simplified Worker Communication
```javascript
// worker.js
import { expose } from 'comlink';

const api = {
    async processData(data) {
        // Heavy computation
        return processedData;
    }
};

expose(api);

// React component
import { wrap } from 'comlink';

function DataProcessor() {
    const [workerApi, setWorkerApi] = useState(null);

    useEffect(() => {
        const worker = new Worker('/worker.js');
        const api = wrap(worker);
        setWorkerApi(api);

        return () => worker.terminate();
    }, []);

    const process = async (data) => {
        if (workerApi) {
            const result = await workerApi.processData(data);
            return result;
        }
    };
}
```

## Asynchronous Patterns

### useEffect with Cleanup
```javascript
function DataFetcher({ userId }) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(false);

    useEffect(() => {
        let cancelled = false;

        const fetchData = async () => {
            setLoading(true);
            try {
                const result = await fetchUserData(userId);
                if (!cancelled) {
                    setData(result);
                }
            } finally {
                if (!cancelled) {
                    setLoading(false);
                }
            }
        };

        fetchData();

        return () => {
            cancelled = true;
        };
    }, [userId]);

    return loading ? <Spinner /> : <DataView data={data} />;
}
```

### AbortController for Request Cancellation
```javascript
function ApiComponent({ query }) {
    const [data, setData] = useState(null);

    useEffect(() => {
        const controller = new AbortController();

        const fetchData = async () => {
            try {
                const response = await fetch(`/api/search?q=${query}`, {
                    signal: controller.signal
                });
                const result = await response.json();
                setData(result);
            } catch (error) {
                if (error.name !== 'AbortError') {
                    console.error('Fetch error:', error);
                }
            }
        };

        fetchData();

        return () => {
            controller.abort();
        };
    }, [query]);
}
```

## State Management Parallelism

### useTransition for Smooth Updates
```javascript
function SearchComponent() {
    const [query, setQuery] = useState('');
    const [suggestions, setSuggestions] = useState([]);
    const [isPending, startTransition] = useTransition();

    const updateQuery = (newQuery) => {
        setQuery(newQuery);

        // Non-urgent suggestion update
        startTransition(async () => {
            const newSuggestions = await fetchSuggestions(newQuery);
            setSuggestions(newSuggestions);
        });
    };

    return (
        <div>
            <input
                value={query}
                onChange={(e) => updateQuery(e.target.value)}
            />
            {isPending && <Spinner />}
            <SuggestionsList suggestions={suggestions} />
        </div>
    );
}
```

### useDeferredValue for Debouncing
```javascript
function SearchResults({ query }) {
    const deferredQuery = useDeferredValue(query);
    const isStale = query !== deferredQuery;

    return (
        <div>
            {isStale && <Spinner />}
            <Results query={deferredQuery} />
        </div>
    );
}
```

## Performance Optimization

### Memoization to Prevent Unnecessary Work
```javascript
const ExpensiveComponent = memo(({ data }) => {
    const processedData = useMemo(() => {
        return expensiveTransformation(data);
    }, [data]);

    return <div>{processedData}</div>;
});

// Memoize callback functions
const handleClick = useCallback((item) => {
    processItem(item);
}, []);
```

### Virtual Scrolling for Large Lists
```javascript
import { FixedSizeList as List } from 'react-window';

function VirtualizedList({ items }) {
    const Row = ({ index, style }) => (
        <div style={style}>
            <ItemComponent item={items[index]} />
        </div>
    );

    return (
        <List
            height={600}
            itemCount={items.length}
            itemSize={50}
            width="100%"
        >
            {Row}
        </List>
    );
}
```

## Best Practices

### Avoid Blocking the Main Thread
```javascript
// Bad - blocking operation
function processLargeArray(array) {
    return array.map(expensiveTransform); // Blocks UI
}

// Good - chunked processing
function* processInChunks(array, chunkSize = 100) {
    for (let i = 0; i < array.length; i += chunkSize) {
        const chunk = array.slice(i, i + chunkSize);
        yield chunk.map(expensiveTransform);
        await new Promise(resolve => setTimeout(resolve, 0));
    }
}
```

### Proper Error Handling
```javascript
function AsyncComponent() {
    const [error, setError] = useState(null);
    const [data, setData] = useState(null);

    useEffect(() => {
        let cancelled = false;

        fetchData()
            .then(result => {
                if (!cancelled) setData(result);
            })
            .catch(err => {
                if (!cancelled) setError(err);
            });

        return () => {
            cancelled = true;
        };
    }, []);

    if (error) return <ErrorDisplay error={error} />;
    // ... render data
}
```

## Testing Parallel Code

### Mocking Async Operations
```javascript
// Test concurrent updates
test('handles rapid state changes', async () => {
    render(<Counter />);

    const button = screen.getByRole('button');
    fireEvent.click(button);
    fireEvent.click(button);
    fireEvent.click(button);

    // Wait for batched update
    await waitFor(() => {
        expect(screen.getByText('3')).toBeInTheDocument();
    });
});

// Test transition states
test('shows pending state during transition', async () => {
    render(<SearchComponent />);

    fireEvent.change(screen.getByRole('textbox'), {
        target: { value: 'test' }
    });

    expect(screen.getByRole('progressbar')).toBeInTheDocument();
});
```

## Browser Compatibility

- Concurrent features require React 18+
- Web Workers have broad browser support
- Consider polyfills for older browsers
- Test performance across different devices