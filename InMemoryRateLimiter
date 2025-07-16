
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.atomic.AtomicInteger;

public class InMemoryRateLimiter {
    private final ConcurrentHashMap<String, AtomicInteger> counters = new ConcurrentHashMap<>();
    private final ConcurrentHashMap<String, Long> lastResetTimes = new ConcurrentHashMap<>();
    private final ConcurrentHashMap<String, Object> locks = new ConcurrentHashMap<>();
    private final long windowMillis;

    public InMemoryRateLimiter(long windowMillis) {
        this.windowMillis = windowMillis;
    }

    public boolean tryAcquire(String key, int limit) {
        long now = System.currentTimeMillis();
        Object lock = locks.computeIfAbsent(key, k -> new Object());

        synchronized (lock) {
            long lastReset = lastResetTimes.getOrDefault(key, now);
            if (now - lastReset > windowMillis) {
                counters.put(key, new AtomicInteger(0));
                lastResetTimes.put(key, now);
            }

            AtomicInteger current = counters.computeIfAbsent(key, k -> new AtomicInteger(0));
            return current.incrementAndGet() <= limit;
        }
    }
}

