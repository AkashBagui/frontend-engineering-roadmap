# Uber Frontend Interview Guide

## Interview Process

Uber's frontend interview process:

1. **Recruiter Call** (30 min) - Background, expectations
2. **Technical Phone Screen** (60 min) - Frontend coding
3. **On-site** (4-5 rounds):
   - **Coding** (45 min) - Algorithms
   - **Frontend Coding** (45 min) - React/JavaScript
   - **System Design** (45 min) - Frontend architecture
   - **Machine Coding** (60 min) - Build a UI from scratch
   - **Behavioral** (45 min) - Uber values

## Frontend Engineering Focus

### 1. Performance at Scale

Uber operates at massive scale. Performance is critical.

**Key areas:**
- Map rendering optimization (WebGL, Canvas)
- Real-time location tracking (WebSocket)
- Large data visualization (thousands of drivers)
- Offline resilience for rides (network drops)

```javascript
// Example: Efficient map marker rendering
class MarkerManager {
  constructor(map) {
    this.map = map;
    this.markers = new Map();
    this.visibleMarkers = [];
  }
  
  updateMarkers(drivers) {
    const bounds = this.map.getBounds();
    
    // Only render markers in viewport
    const visibleDrivers = drivers.filter(d => bounds.contains(d.position));
    
    // Remove markers no longer visible
    this.visibleMarkers.forEach(marker => {
      if (!visibleDrivers.find(d => d.id === marker.id)) {
        marker.remove();
      }
    });
    
    // Add new markers
    visibleDrivers.forEach(driver => {
      if (!this.markers.has(driver.id)) {
        const marker = this.createMarker(driver);
        this.markers.set(driver.id, marker);
      }
    });
    
    this.visibleMarkers = visibleDrivers;
  }
  
  createMarker(driver) {
    // Use canvas-based marker for performance
    // instead of DOM-based markers
  }
}
```

### 2. Real-Time Data

```javascript
// WebSocket management for live tracking
function useWebSocket(url) {
  const [data, setData] = useState(null);
  const [connected, setConnected] = useState(false);
  const wsRef = useRef(null);
  
  useEffect(() => {
    function connect() {
      wsRef.current = new WebSocket(url);
      
      wsRef.current.onopen = () => setConnected(true);
      wsRef.current.onclose = () => {
        setConnected(false);
        // Auto-reconnect after 3 seconds
        setTimeout(connect, 3000);
      };
      wsRef.current.onerror = () => wsRef.current?.close();
      wsRef.current.onmessage = (event) => {
        setData(JSON.parse(event.data));
      };
    }
    
    connect();
    return () => wsRef.current?.close();
  }, [url]);
  
  return { data, connected };
}
```

### 3. Machine Coding Round

Uber often has a machine coding round where you build a UI from scratch.

**Common problems:**
- Real-time ride tracking dashboard
- Driver/partner onboarding form (multi-step)
- Interactive map with markers and info windows
- Trip fare calculator with dynamic pricing
- Route display with waypoints

**Sample: Build a Ride Fare Calculator**
```typescript
function FareCalculator() {
  const [distance, setDistance] = useState(0);
  const [time, setTime] = useState(0);
  const [type, setType] = useState('uberX');
  const [fare, setFare] = useState(null);
  
  const pricing = {
    uberX: { base: 2.50, perMin: 0.35, perMile: 1.15, min: 5.65 },
    uberXL: { base: 3.50, perMin: 0.55, perMile: 1.75, min: 8.25 },
    comfort: { base: 3.00, perMin: 0.45, perMile: 1.50, min: 7.00 },
  };
  
  const calculateFare = useCallback(() => {
    const p = pricing[type];
    let total = p.base + (time * p.perMin) + (distance * p.perMile);
    
    // Surge pricing simulation (1.0x - 2.0x)
    const surge = 1.0 + (Math.random() * 0.5);
    total *= surge;
    
    return Math.max(total, p.min);
  }, [distance, time, type]);
  
  return (
    <div className="fare-calculator">
      <h2>Fare Estimate</h2>
      
      <label>
        Distance (miles):
        <input type="range" min="0" max="50" value={distance}
          onChange={e => setDistance(Number(e.target.value))} />
        {distance} mi
      </label>
      
      <label>
        Estimated Time (min):
        <input type="range" min="0" max="120" value={time}
          onChange={e => setTime(Number(e.target.value))} />
        {time} min
      </label>
      
      <div className="ride-types">
        {Object.entries(pricing).map(([key, value]) => (
          <button key={key}
            className={type === key ? 'selected' : ''}
            onClick={() => setType(key)}>
            {key}: ${value.base} base
          </button>
        ))}
      </div>
      
      <div className="fare-result">
        Estimated Fare: ${calculateFare().toFixed(2)}
      </div>
    </div>
  );
}
```

## System Design for Scale

**Typical problems:**
- Design Uber's rider app home screen
- Design a real-time driver tracking system
- Design Uber Eats restaurant ordering
- Design a trip planning interface

**Key considerations:**
- **Geospatial rendering** - Efficiently display hundreds of nearby drivers
- **Real-time updates** - WebSocket connections for live tracking
- **Offline support** - Ride booking must work with intermittent connectivity
- **Cross-platform** - Same design across web and mobile
- **Accessibility** - Must be usable by all riders/drivers

## Preparation Strategy

### Technical:
- JavaScript fundamentals (closures, event loop, promises)
- React (hooks, state management, performance)
- Map integration (Mapbox, Google Maps)
- WebSocket and real-time communication
- Performance optimization (virtual scrolling, canvas rendering)
- CSS animations and transitions

### System Design:
- Practice designing data-intensive UIs
- Consider offline-first architecture
- Understand CDN and caching strategies
- Know how to handle real-time data at scale

### Behavioral (Uber Values):
Uber's values include:
- **Customer obsession** - Rider and driver experience
- **Door is always open** - Honest communication
- **Build with heart** - Products that matter
- **Think long-term** - Sustainability at scale
- **Celebrate differences** - Diversity and inclusion

## Tips from Interviewees

- **Performance is everything** - Always mention performance
- **Real-time experience helps** - Emphasize any WebSocket/live data work
- **Map experience is a plus** - Even basic map integration knowledge helps
- **Mobile responsiveness** - Uber is mobile-first
- **Handle loading states** - Uber's app has many async operations
- **Think about edge cases** - Network failure, GPS inaccuracies, surge pricing

## Common Mistakes

- Not considering offline/connectivity issues
- Weak understanding of WebSockets
- Not optimizing for mobile (touch events, small screens)
- Ignoring accessibility (unlikely for Uber's diverse user base)
- Not preparing for the machine coding round (simulator-like environment)
