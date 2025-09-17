import React, { useState, useEffect } from 'react';
import { MapContainer, TileLayer, Marker, Popup } from 'react-leaflet';
import * as L from 'leaflet';
import 'leaflet/dist/leaflet.css';

type LatLngTuple = L.LatLngTuple;

interface Feature {
  position: LatLngTuple;
  name: string;
  tags: Record<string, string>;
}

const topicMappings: Record<string, string> = {
  bus: '["highway"="bus_stop"]',
  stop: '["highway"="bus_stop"]',
  school: '["amenity"="school"]',
  university: '["amenity"="university"]',
  college: '["amenity"="college"]',
  pathway: '["highway"="pathway"]',
  footway: '["highway"="footway"]',
  sidewalk: '["highway"="footway"]',
  cycleway: '["highway"="cycleway"]',
  bike: '["highway"="cycleway"]',
  restaurant: '["amenity"="restaurant"]',
  cafe: '["amenity"="cafe"]',
  bar: '["amenity"="bar"]',
  park: '["leisure"="park"]',
  hospital: '["amenity"="hospital"]',
  clinic: '["amenity"="clinic"]',
  shop: '["shop"]',
  store: '["shop"]',
  atm: '["amenity"="atm"]',
  bank: '["amenity"="bank"]',
  hotel: '["tourism"="hotel"]',
};

const supportedTopics = Object.keys(topicMappings).join(', ');

function parseQuery(query: string): { tagFilters: string[]; city: string } {
  const lower = query.toLowerCase().trim();
  const inMatch = lower.match(/(.+?)\s+in\s+(.+)$/i);
  if (!inMatch) {
    throw new Error('Query format: "topics in city", e.g. "bus stops in Bordeaux"');
  }
  const topicPart = inMatch[1].trim();
  const cityPart = inMatch[2].trim();
  if (!topicPart || !cityPart) {
    throw new Error('Both topic(s) and city are required.');
  }
  // Split topic part by common separators like 'and', ',' and spaces
  const topicWords = topicPart
    .split(/[,\s]+and[,\s]+|[,\s]+|\band\b/)
    .map((w) => w.trim())
    .filter((w) => w.length > 0);
  const tagFiltersSet = new Set<string>();
  topicWords.forEach((word) => {
    const mapping = topicMappings[word];
    if (mapping) {
      tagFiltersSet.add(mapping);
    }
  });
  if (tagFiltersSet.size === 0) {
    throw new Error(
      `No matching topics found in "${topicPart}". Supported: ${supportedTopics}`
    );
  }
  return {
    tagFilters: Array.from(tagFiltersSet),
    city: cityPart,
  };
}

function getZoomForBbox(
  south: number,
  west: number,
  north: number,
  east: number
): number {
  const latDiff = north - south;
  const lonDiff = east - west;
  const avgLat = (south + north) / 2;
  const adjustedLonDiff = lonDiff / Math.cos((avgLat * Math.PI) / 180);
  const maxDiff = Math.max(latDiff, adjustedLonDiff);
  if (maxDiff <= 0.001) return 18;
  if (maxDiff <= 0.01) return 16;
  if (maxDiff <= 0.1) return 13;
  if (maxDiff <= 1) return 10;
  if (maxDiff <= 5) return 8;
  if (maxDiff <= 10) return 6;
  return 4;
}

export default function Home() {
  const [inputValue, setInputValue] = useState<string>('bus stops in Bordeaux');
  const [features, setFeatures] = useState<Feature[]>([]);
  const [loading, setLoading] = useState<boolean>(false);
  const [error, setError] = useState<string>('');
  const [center, setCenter] = useState<LatLngTuple>([51.505, -0.09]);
  const [zoom, setZoom] = useState<number>(13);
  const [leafletMap, setLeafletMap] = useState<L.Map | null>(null);

  // Fix Leaflet default icons to load from CDN (common issue in bundlers like Next.js/Webpack)
  useEffect(() => {
    const iconRetinaUrl =
      'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/images/marker-icon-2x.png';
    const iconUrl =
      'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/images/marker-icon.png';
    const shadowUrl =
      'https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/images/marker-shadow.png';
    L.Icon.Default.mergeOptions({
      iconRetinaUrl,
      iconUrl,
      shadowUrl,
    });
  }, []);

  // Update map view when center or zoom changes
  useEffect(() => {
    if (leafletMap) {
      leafletMap.setView(center, zoom, { animate: true });
    }
  }, [center, zoom, leafletMap]);

  const handleSearch = async () => {
    setError('');
    setFeatures([]);
    setLoading(true);
    try {
      const parsed = parseQuery(inputValue);
      const { tagFilters, city } = parsed;

      // Step 1: Geocode city with Nominatim to get bbox and center
      const nomUrl = `https://nominatim.openstreetmap.org/search?format=json&q=${encodeURIComponent(
        city
      )}&limit=1&addressdetails=1`;
      const nomRes = await fetch(nomUrl);
      if (!nomRes.ok) {
        throw new Error(`Geocoding failed: ${nomRes.status}`);
      }
      const nomData = (await nomRes.json()) as any[];
      if (nomData.length === 0) {
        throw new Error(`No location found for city '${city}'. Try a more specific name.`);
      }
      const place = nomData[0];
      const lat = parseFloat(place.lat);
      const lon = parseFloat(place.lon);
      const boundingbox = place.boundingbox as [string, string, string, string];
      const south = parseFloat(boundingbox[0]);
      const north = parseFloat(boundingbox[1]);
      const west = parseFloat(boundingbox[2]);
      const east = parseFloat(boundingbox[3]);
      const bbox = `${south},${west},${north},${east}`;

      // Calculate adaptive zoom based on bbox size
      const calculatedZoom = getZoomForBbox(south, west, north, east);
      setCenter([lat, lon]);
      setZoom(calculatedZoom);

      // Step 2: Construct and send Overpass API query
      const nodeQueries = tagFilters
        .map((filter) => `node${filter}(${bbox})`)
        .join('; ');
      const query = `[out:json][timeout:30];\n(\n  ${nodeQueries}\n);\nout geom;\n`;
      const opUrl = `https://overpass-api.de/api/interpreter?data=${encodeURIComponent(query)}`;
      const opRes = await fetch(opUrl);
      if (!opRes.ok) {
        throw new Error(`Overpass API error: ${opRes.statusText}`);
      }
      const opData = await opRes.json();
      const elements = opData.elements || [];

      // Step 3: Filter and transform to features (only nodes for simplicity)
      const newFeatures: Feature[] = elements
        .filter((el: any) => el.type === 'node' && typeof el.lat === 'number' && typeof el.lon === 'number')
        .map((el: any) => ({
          position: [el.lat, el.lon] as LatLngTuple,
          name: el.tags?.name || 'Unnamed Feature',
          tags: el.tags || {},
        }));

      setFeatures(newFeatures);
    } catch (err: any) {
      setError(err.message || 'An unexpected error occurred.');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-gray-50 via-white to-blue-50 flex flex-col overflow-hidden">
      {/* Header */}
      <header className="bg-gradient-to-r from-indigo-600 via-purple-600 to-pink-600 text-white shadow-2xl p-6 md:p-8">
        <div className="max-w-7xl mx-auto text-center md:text-left">
          <h1 className="text-3xl md:text-5xl font-black mb-2 drop-shadow-lg">
            🌍 OSM Data Explorer
          </h1>
          <p className="text-lg md:text-xl opacity-95 max-w-3xl mx-auto md:mx-0 leading-relaxed">
            Describe what you want to see in natural language (e.g., &quot;bus stops in Bordeaux&quot; or &quot;schools and parks in Paris&quot;) and watch it appear on a beautiful Voyager map powered by OpenStreetMap.
          </p>
          <p className="text-sm md:text-base mt-4 opacity-80 italic">
            Fetches live data via Overpass API. Zoom adjusts automatically to your city!
          </p>
        </div>
      </header>

      {/* Main Content with Overlaid Controls */}
      <main className="flex-1 relative overflow-hidden">
        {/* Search Panel - Centered Overlay */}
        <div className="absolute inset-0 flex items-center justify-center p-4 z-20 pointer-events-none">
          <div className="bg-white/95 backdrop-blur-xl rounded-3xl shadow-3xl border border-white/50 p-6 md:p-8 w-full max-w-4xl mx-4 max-h-[90vh] overflow-y-auto pointer-events-auto transform transition-all duration-300 hover:shadow-4xl">
            {/* Input Section */}
            <div className="space-y-6">
              <div>
                <label className="block text-gray-800 text-xl font-bold mb-4 text-center md:text-left">
                  🔍 What do you want to explore?
                </label>
                <input
                  type="text"
                  value={inputValue}
                  onChange={(e) => setInputValue(e.target.value)}
                  onKeyDown={(e) => {
                    if (e.key === 'Enter' && !loading) {
                      handleSearch();
                    }
                  }}
                  className="w-full px-6 py-4 text-lg bg-gradient-to-r from-gray-50 to-blue-50 border-2 border-gray-300 rounded-2xl focus:outline-none focus:ring-4 focus:ring-indigo-500 focus:border-transparent shadow-xl transition-all duration-300 placeholder-gray-500 font-medium"
                  placeholder={`Try: &quot;${supportedTopics.slice(0, 50)}... in your city&quot;`}
                  disabled={loading}
                />
              </div>

              {/* Search Button */}
              <button
                onClick={handleSearch}
                disabled={loading}
                className="w-full py-4 px-8 bg-gradient-to-r from-emerald-500 via-teal-500 to-cyan-500 text-white font-extrabold text-lg rounded-2xl shadow-2xl hover:shadow-3xl transform hover:scale-105 active:scale-95 transition-all duration-300 disabled:from-gray-400 disabled:to-gray-500 disabled:cursor-not-allowed disabled:transform-none flex items-center justify-center gap-3 uppercase tracking-wide letter-spacing-1"
              >
                {loading ? (
                  <>
                    <div className="animate-spin rounded-full h-6 w-6 border-2 border-white border-t-transparent" />
                    Searching OpenStreetMap...
                  </>
                ) : (
                  <>
                    🚀 Launch Query
                  </>
                )}
              </button>

              {/* Error Display */}
              {error && (
                <div className="p-6 bg-gradient-to-r from-red-50 to-pink-50 border-2 border-red-200 rounded-2xl shadow-md">
                  <div className="flex items-center gap-3 mb-2">
                    <span className="text-2xl">⚠️</span>
                    <h3 className="font-bold text-red-800 text-lg">Query Issue</h3>
                  </div>
                  <p className="text-red-700 leading-relaxed whitespace-pre-wrap">{error}</p>
                </div>
              )}

              {/* Supported Topics Info */}
              <div className="p-4 bg-blue-50/50 border border-blue-200 rounded-2xl text-sm md:text-base space-y-2">
                <p className="font-semibold text-blue-800 flex items-center gap-2">
                  📋 Supported Topics (mix multiple!)
                </p>
                <p className="text-blue-700 pl-6">{supportedTopics}</p>
                <p className="text-gray-600 text-xs italic mt-2">
                  Topics are case-insensitive and can be combined (e.g., &quot;bus and school in Tokyo&quot;).
                </p>
              </div>

              {/* Footer Info */}
              <div className="pt-6 border-t border-gray-200 text-center text-xs md:text-sm space-y-2 text-gray-500">
                <p>
                  Powered by{' '}
                  <a
                    href="https://overpass-api.de/"
                    target="_blank"
                    rel="noopener noreferrer"
                    className="underline hover:text-indigo-600 font-medium transition-colors"
                  >
                    Overpass API
                  </a>{' '}for data,{' '}
                  <a
                    href="https://nominatim.org/"
                    target="_blank"
                    rel="noopener noreferrer"
                    className="underline hover:text-indigo-600 font-medium transition-colors"
                  >
                    Nominatim
                  </a>{' '}for geocoding, and{' '}
                  <a
                    href="https://www.openstreetmap.org/"
                    target="_blank"
                    rel="noopener noreferrer"
                    className="underline hover:text-indigo-600 font-medium transition-colors"
                  >
                    OpenStreetMap
                  </a>{' '}for the base data.
                </p>
                <p>
                  🗺️ Map styled with{' '}
                  <a
                    href="https://carto.com/basemaps/voyager"
                    target="_blank"
                    rel="noopener noreferrer"
                    className="underline hover:text-indigo-600 font-medium transition-colors"
                  >
                    CartoDB Voyager
                  </a>
                </p>
              </div>
            </div>
          </div>
        </div>

        {/* Map */}
        <MapContainer
          center={center}
          zoom={zoom}
          style={{ height: '100vh', width: '100%' }}
          className="z-0 rounded-none"
          whenCreated={setLeafletMap}
          scrollWheelZoom={true}
          doubleClickZoom={true}
          dragging={true}
        >
          <TileLayer
            attribution='&amp;copy; <a href=&quot;https://www.openstreetmap.org/copyright&quot;>OpenStreetMap</a> contributors &amp;copy; <a href=&quot;https://carto.com/attributions&quot;>CARTO</a>'
            url="https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png"
          />

          {/* Dynamic Markers */}
          {features.map((f, i) => (
            <Marker key={i} position={f.position}>
              <Popup maxWidth={350} minWidth={250} closeButton={true}>
                <div style={{ fontFamily: 'system-ui, -apple-system, sans-serif', lineHeight: '1.4' }}>
                  <h3
                    style={{
                      fontSize: '18px',
                      fontWeight: 'bold',
                      marginBottom: '12px',
                      color: '#1e293b',
                      borderBottom: '2px solid #e2e8f0',
                      paddingBottom: '6px',
                    }}
                  >
                    📍 {f.name}
                  </h3>
                  {Object.entries(f.tags).length > 0 ? (
                    <div style={{ fontSize: '14px' }}>
                      {Object.entries(f.tags).map(([k, v], j) => (
                        <div
                          key={j}
                          style={{
                            marginBottom: '8px',
                            padding: '6px',
                            backgroundColor: '#f8fafc',
                            borderRadius: '6px',
                            borderLeft: '3px solid #3b82f6',
                          }}
                        >
                          <span style={{ color: '#374151', fontWeight: '600', fontSize: '13px' }}>
                            {k}:
                          </span>{' '}
                          <span style={{ color: '#4b5563' }}>{v}</span>
                        </div>
                      ))}
                    </div>
                  ) : (
                    <p style={{ fontSize: '14px', color: '#6b7280', fontStyle: 'italic', textAlign: 'center', padding: '20px', backgroundColor: '#fef3c7' }}>
                      No additional details available. This is a basic point of interest.
                    </p>
                  )}
                </div>
              </Popup>
            </Marker>
          ))}
        </MapContainer>

        {/* Results Counter and Fit Button - Bottom Right */}
        {features.length > 0 && (
          <div className="absolute bottom-6 right-6 z-30 pointer-events-auto">
            <div className="bg-white/95 backdrop-blur-xl rounded-2xl shadow-2xl p-4 md:p-6 border border-gray-200/50 space-y-3 min-w-[200px] transform transition-all duration-300 hover:shadow-3xl">
              <div className="flex items-center justify-between gap-2 mb-3">
                <span className="text-2xl font-bold text-green-600">🎯</span>
                <span className="text-lg font-bold text-gray-800">{features.length}</span>
                <span className="text-sm text-gray-600">results</span>
              </div>
              <button
                onClick={() => {
                  if (leafletMap && features.length > 0) {
                    const bounds = L.latLngBounds(features.map((f) => f.position));
                    leafletMap.fitBounds(bounds, { padding: L.point(50, 50), duration: 1 });
                  }
                }}
                className="w-full py-3 px-4 bg-gradient-to-r from-blue-500 to-indigo-600 text-white font-semibold rounded-xl shadow-lg hover:shadow-xl transform hover:scale-105 transition-all duration-200 flex items-center justify-center gap-2 text-sm uppercase tracking-wide"
              >
                📐 Fit to All Markers
              </button>
            </div>
          </div>
        )}

        {/* Loading Overlay if Needed */}
        {loading && (
          <div className="absolute inset-0 bg-black/20 backdrop-blur-sm flex items-center justify-center z-40">
            <div className="bg-white/90 backdrop-blur-xl rounded-3xl p-8 shadow-3xl text-center space-y-4 border-2 border-indigo-200">
              <div className="animate-spin rounded-full h-12 w-12 mx-auto border-4 border-indigo-500 border-t-transparent mb-4" />
              <p className="text-xl font-semibold text-gray-700">Fetching data from OpenStreetMap...</p>
              <p className="text-sm text-gray-500">This may take a few seconds for large areas.</p>
            </div>
          </div>
        )}
      </main>
    </div>
  );
}
