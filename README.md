# Ip-tracker
import { useState, useEffect, useRef } from "react";

const FONT = "'Courier New', Courier, monospace";

const DEMO_IP = {
  query: "106.221.197.174", country: "India", countryCode: "IN",
  regionName: "Maharashtra", city: "Mumbai", zip: "400001",
  lat: 19.076, lon: 72.8777, timezone: "Asia/Kolkata",
  isp: "Bharti Airtel Ltd.", org: "AS24560 Bharti Airtel Ltd.",
  as: "AS24560", asname: "AIRTELBROADBAND-AS-AP",
  mobile: true, proxy: false, hosting: false, _demo: true,
};

const S = {
  root: { background: "#060b06", minHeight: "100vh", fontFamily: FONT, color: "#00ff41", position: "relative", overflow: "hidden" },
  grid: { position: "fixed", inset: 0, backgroundImage: "linear-gradient(rgba(0,255,65,0.03) 1px,transparent 1px),linear-gradient(90deg,rgba(0,255,65,0.03) 1px,transparent 1px)", backgroundSize: "44px 44px", pointerEvents: "none", zIndex: 0 },
  scan: { position: "fixed", inset: 0, background: "repeating-linear-gradient(0deg,transparent,transparent 2px,rgba(0,0,0,0.06) 2px,rgba(0,0,0,0.06) 4px)", pointerEvents: "none", zIndex: 1 },
  wrap: { position: "relative", zIndex: 2, maxWidth: 820, margin: "0 auto", padding: "36px 18px 60px" },
  hdr: { textAlign: "center", marginBottom: 30 },
  eyebrow: { fontSize: 10, letterSpacing: 8, color: "#00ff4188", marginBottom: 6, textTransform: "uppercase" },
  h1: { fontSize: 32, fontWeight: "bold", letterSpacing: 3, color: "#00ff41", textShadow: "0 0 28px #00ff4188,0 0 70px #00ff4133", marginBottom: 6 },
  sub: { fontSize: 11, color: "#00ff4155", letterSpacing: 3 },
  pulse: { width: 7, height: 7, borderRadius: "50%", background: "#00ff41", display: "inline-block", marginRight: 7, boxShadow: "0 0 8px #00ff41", animation: "pulse 1.5s infinite" },

  // two column layout
  cols: { display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16, marginBottom: 16 },

  inp: { flex: 1, minWidth: 160, background: "transparent", border: "1px solid #00ff4144", borderRadius: 2, color: "#00ff41", fontFamily: FONT, fontSize: 13, padding: "11px 14px", outline: "none", letterSpacing: 1 },
  row: { display: "flex", gap: 10, marginBottom: 20, flexWrap: "wrap" },
  btn: { background: "#00ff4111", border: "1px solid #00ff41", color: "#00ff41", fontFamily: FONT, fontSize: 11, letterSpacing: 3, padding: "11px 18px", cursor: "pointer", textTransform: "uppercase", borderRadius: 2 },
  btn2: { background: "transparent", border: "1px solid #00ff4155", color: "#00ff4188", fontFamily: FONT, fontSize: 11, letterSpacing: 2, padding: "11px 18px", cursor: "pointer", textTransform: "uppercase", borderRadius: 2 },
  btnGps: { background: "#001a2211", border: "1px solid #00ccff", color: "#00ccff", fontFamily: FONT, fontSize: 11, letterSpacing: 2, padding: "11px 18px", cursor: "pointer", textTransform: "uppercase", borderRadius: 2 },

  card: { border: "1px solid #00ff4120", borderRadius: 3, background: "#040804", padding: "22px 26px", boxShadow: "0 0 24px #00ff4106" },
  cardBlue: { border: "1px solid #00ccff22", borderRadius: 3, background: "#020810", padding: "22px 26px", boxShadow: "0 0 24px #00ccff06" },
  ctitle: { fontSize: 9, letterSpacing: 6, color: "#00ff4155", textTransform: "uppercase", marginBottom: 16, borderBottom: "1px solid #00ff4112", paddingBottom: 8 },
  ctitleBlue: { fontSize: 9, letterSpacing: 6, color: "#00ccff66", textTransform: "uppercase", marginBottom: 16, borderBottom: "1px solid #00ccff15", paddingBottom: 8 },

  g2: { display: "grid", gridTemplateColumns: "1fr 1fr", gap: "12px 20px" },
  fl: { display: "flex", flexDirection: "column", gap: 3 },
  lbl: { fontSize: 8, letterSpacing: 3, color: "#00ff4144", textTransform: "uppercase" },
  lblBlue: { fontSize: 8, letterSpacing: 3, color: "#00ccff44", textTransform: "uppercase" },
  val: { fontSize: 13, color: "#00ff41", letterSpacing: 0.5, wordBreak: "break-all" },
  valBlue: { fontSize: 13, color: "#00ccff", letterSpacing: 0.5, wordBreak: "break-all" },
  big: { fontSize: 20, fontWeight: "bold", color: "#00ff41", textShadow: "0 0 12px #00ff4155", letterSpacing: 1 },
  bigBlue: { fontSize: 20, fontWeight: "bold", color: "#00ccff", textShadow: "0 0 12px #00ccff55", letterSpacing: 1 },

  bdg: { display: "inline-block", border: "1px solid #00ff4133", borderRadius: 2, padding: "2px 7px", fontSize: 9, letterSpacing: 2, color: "#00ff4166", marginRight: 4, marginTop: 3 },
  bdgGps: { display: "inline-block", border: "1px solid #00ccff33", borderRadius: 2, padding: "2px 7px", fontSize: 9, letterSpacing: 2, color: "#00ccff88", marginRight: 4, marginTop: 3 },
  bdgGood: { display: "inline-block", border: "1px solid #44ff8833", borderRadius: 2, padding: "2px 7px", fontSize: 9, letterSpacing: 2, color: "#44ff88aa", marginRight: 4, marginTop: 3 },
  bdgWarn: { display: "inline-block", border: "1px solid #ff884433", borderRadius: 2, padding: "2px 7px", fontSize: 9, letterSpacing: 2, color: "#ff8844aa", marginRight: 4, marginTop: 3 },

  mapBox: { marginTop: 16, border: "1px solid #00ff4115", borderRadius: 2, background: "#020502", padding: "16px", display: "flex", flexDirection: "column", alignItems: "center", gap: 5 },
  mapBoxBlue: { marginTop: 16, border: "1px solid #00ccff15", borderRadius: 2, background: "#010508", padding: "16px", display: "flex", flexDirection: "column", alignItems: "center", gap: 5 },

  // compare section
  cmpCard: { border: "1px solid #ffffff11", borderRadius: 3, background: "#08080a", padding: "22px 26px", marginBottom: 16 },
  cmpTitle: { fontSize: 9, letterSpacing: 6, color: "#ffffff44", textTransform: "uppercase", marginBottom: 16, borderBottom: "1px solid #ffffff0a", paddingBottom: 8 },
  cmpRow: { display: "flex", justifyContent: "space-between", alignItems: "center", padding: "8px 0", borderBottom: "1px solid #ffffff08", fontSize: 12 },
  cmpLabel: { color: "#ffffff33", letterSpacing: 1, fontSize: 10 },
  cmpIp: { color: "#00ff4188" },
  cmpGps: { color: "#00ccffaa" },

  demo: { background: "#110e00", border: "1px solid #ffaa0022", borderRadius: 3, padding: "9px 13px", marginBottom: 16, fontSize: 11, color: "#ffaa0077", lineHeight: 1.8 },
  err: { background: "#110000", border: "1px solid #ff444422", borderRadius: 3, padding: "9px 13px", marginBottom: 12, fontSize: 11, color: "#ff4444aa" },
  gpsErr: { background: "#00080d", border: "1px solid #00ccff22", borderRadius: 3, padding: "9px 13px", marginBottom: 12, fontSize: 11, color: "#00ccff66" },
  logBox: { fontSize: 11, color: "#00ff4144", lineHeight: 2, letterSpacing: 0.3, maxHeight: 200, overflowY: "auto" },
  loading: { textAlign: "center", padding: "22px", fontSize: 11, letterSpacing: 4, color: "#00ff4144" },
};

const css = `
@keyframes pulse{0%,100%{opacity:1;box-shadow:0 0 8px #00ff41}50%{opacity:.3;box-shadow:none}}
@keyframes fadeUp{from{opacity:0;transform:translateY(5px)}to{opacity:1;transform:none}}
@keyframes blink{0%,100%{opacity:1}50%{opacity:0}}
.ip-inp:focus{border-color:#00ff41!important;box-shadow:0 0 12px #00ff4115!important;}
.ip-btn:hover:not(:disabled){filter:brightness(1.3);}
.ip-btn:disabled{opacity:.35;cursor:not-allowed;}
.fade{animation:fadeUp 0.3s ease;}
@media(max-width:600px){.two-col{grid-template-columns:1fr!important;}}
`;

// reverse geocode using open nominatim
const reverseGeocode = async (lat, lon) => {
  try {
    const r = await fetch(
      `https://nominatim.openstreetmap.org/reverse?lat=${lat}&lon=${lon}&format=json`,
      { headers: { "Accept-Language": "en" } }
    );
    const j = await r.json();
    const a = j.address || {};
    return {
      city:    a.city || a.town || a.village || a.suburb || "N/A",
      state:   a.state || "N/A",
      country: a.country || "N/A",
      postcode: a.postcode || "N/A",
      display: j.display_name || "N/A",
    };
  } catch {
    return null;
  }
};

const F = ({ label, value, big, blue, children }) => (
  <div style={S.fl}>
    <span style={blue ? S.lblBlue : S.lbl}>{label}</span>
    {children ?? (
      <span style={big ? (blue ? S.bigBlue : S.big) : (blue ? S.valBlue : S.val)}>
        {value ?? "—"}
      </span>
    )}
  </div>
);

const Card = ({ title, blue, children, style }) => (
  <div style={{ ...(blue ? S.cardBlue : S.card), ...style }} className="fade">
    <div style={blue ? S.ctitleBlue : S.ctitle}>
      {blue ? "◈" : "▸"} {title}
    </div>
    {children}
  </div>
);

export default function IPTracker() {
  const [inp, setInp]         = useState("");
  const [ipData, setIpData]   = useState(null);
  const [gpsData, setGpsData] = useState(null);
  const [ipBusy, setIpBusy]   = useState(false);
  const [gpsBusy, setGpsBusy] = useState(false);
  const [demo, setDemo]       = useState(false);
  const [ipErr, setIpErr]     = useState("");
  const [gpsErr, setGpsErr]   = useState("");
  const [logs, setLogs]       = useState([]);
  const logRef                = useRef([]);

  const log = (msg) => {
    const ts = new Date().toISOString().split("T")[1].slice(0, 12);
    logRef.current = [...logRef.current.slice(-20), `[${ts}]  ${msg}`];
    setLogs([...logRef.current]);
  };

  // ── IP Lookup via ipinfo.io ──────────────────────────────
  const lookupIP = async (ip) => {
    const t = ip?.trim() || "";
    setIpBusy(true); setIpErr(""); setIpData(null); setDemo(false);
    log(`IP lookup → ${t || "self"}`);
    log("Querying ipinfo.io...");
    try {
      const url = t ? `https://ipinfo.io/${t}/json` : `https://ipinfo.io/json`;
      const res = await fetch(url);
      if (!res.ok) throw new Error(`HTTP ${res.status}`);
      const j = await res.json();
      if (j.error) throw new Error(j.error.message || "Lookup failed");

      const [lat, lon] = (j.loc || "0,0").split(",").map(Number);
      const asMatch = (j.org || "").match(/^(AS\d+)\s+(.+)$/);

      const d = {
        query: j.ip, country: j.country, city: j.city || "N/A",
        region: j.region || "N/A", zip: j.postal || "N/A",
        lat, lon, timezone: j.timezone || "N/A",
        isp: asMatch ? asMatch[2] : j.org || "N/A",
        org: j.org || "N/A",
        as: asMatch ? asMatch[1] : "N/A",
        asname: asMatch ? asMatch[2] : "N/A",
      };

      log(`IP resolved        →  ${d.query}`);
      log(`ISP                →  ${d.isp}`);
      log(`IP-based location  →  ${d.city}, ${d.region}, ${d.country}`);
      log(`Coordinates (IP)   →  ${lat}, ${lon}`);
      setIpData(d);
    } catch (e) {
      log(`ipinfo.io blocked (sandbox) — loading demo data`);
      setDemo(true);
      setIpData({ ...DEMO_IP, query: t || DEMO_IP.query });
    } finally {
      setIpBusy(false);
    }
  };

  // ── GPS / Real Location ──────────────────────────────────
  const getGPS = () => {
    setGpsBusy(true); setGpsErr(""); setGpsData(null);
    log("Requesting GPS / device location...");

    if (!navigator.geolocation) {
      setGpsErr("Geolocation not supported by this browser.");
      log("ERROR: Geolocation API unavailable");
      setGpsBusy(false);
      return;
    }

    navigator.geolocation.getCurrentPosition(
      async (pos) => {
        const lat = pos.coords.latitude;
        const lon = pos.coords.longitude;
        const acc = pos.coords.accuracy;

        log(`GPS acquired       →  ${lat.toFixed(5)}, ${lon.toFixed(5)}`);
        log(`Accuracy           →  ±${Math.round(acc)}m`);
        log("Reverse geocoding via Nominatim...");

        const geo = await reverseGeocode(lat, lon);

        if (geo) {
          log(`Real location      →  ${geo.city}, ${geo.state}, ${geo.country}`);
        } else {
          log("Reverse geocode failed — showing coordinates only");
        }

        setGpsData({
          lat: lat.toFixed(6),
          lon: lon.toFixed(6),
          accuracy: Math.round(acc),
          city:    geo?.city    || "N/A",
          state:   geo?.state   || "N/A",
          country: geo?.country || "N/A",
          postcode: geo?.postcode || "N/A",
          display: geo?.display  || `${lat.toFixed(4)}, ${lon.toFixed(4)}`,
        });
        setGpsBusy(false);
      },
      (err) => {
        const msgs = {
          1: "Permission denied — please allow location access.",
          2: "Position unavailable — try again.",
          3: "Request timed out.",
        };
        const msg = msgs[err.code] || "Unknown error";
        log(`GPS ERROR: ${msg}`);
        setGpsErr(msg);
        setGpsBusy(false);
      },
      { enableHighAccuracy: true, timeout: 15000, maximumAge: 0 }
    );
  };

  useEffect(() => {
    log("System initialized");
    log("Mode: IP Geolocation  +  GPS Real Location");
    lookupIP("");
  }, []);

  const mapsLink = (lat, lon) =>
    `https://www.google.com/maps?q=${lat},${lon}`;

  return (
    <div style={S.root}>
      <style>{css}</style>
      <div style={S.grid} />
      <div style={S.scan} />

      <div style={S.wrap}>

        {/* Header */}
        <div style={S.hdr}>
          <div style={S.eyebrow}>Network Intelligence Tool</div>
          <div style={S.h1}>IP + GPS TRACKER</div>
          <div style={S.sub}>
            <span style={S.pulse} />
            IP GEOLOCATION &nbsp;·&nbsp; REAL GPS LOCATION &nbsp;·&nbsp; ASN &nbsp;·&nbsp; ISP
          </div>
        </div>

        {/* Demo notice */}
        {demo && (
          <div style={S.demo}>
            ⚠ DEMO MODE — sandbox blocks external API calls.<br />
            Run locally (<code>npm run dev</code>) for live IP lookups.<br />
            GPS lookup works here in the preview — tap <strong style={{ color: "#ffaa00" }}>GET MY REAL LOCATION</strong> below.
          </div>
        )}

        {/* Input */}
        <div style={S.row}>
          <input
            className="ip-inp"
            style={S.inp}
            placeholder="Enter IP or domain  (blank = your IP)"
            value={inp}
            onChange={e => setInp(e.target.value)}
            onKeyDown={e => e.key === "Enter" && !ipBusy && lookupIP(inp)}
          />
          <button className="ip-btn" style={S.btn}  onClick={() => lookupIP(inp)} disabled={ipBusy}>
            {ipBusy ? "TRACING…" : "TRACE IP"}
          </button>
          <button className="ip-btn" style={S.btn2} onClick={() => { setInp(""); lookupIP(""); }} disabled={ipBusy}>
            MY IP
          </button>
          <button className="ip-btn" style={S.btnGps} onClick={getGPS} disabled={gpsBusy}>
            {gpsBusy ? "LOCATING…" : "⊕ GET MY REAL LOCATION"}
          </button>
        </div>

        {ipErr  && <div style={S.err}    >✗ {ipErr}</div>}
        {gpsErr && <div style={S.gpsErr} >⊕ {gpsErr}</div>}

        {(ipBusy || gpsBusy) && (
          <div style={S.loading}>
            {ipBusy  && "◌  QUERYING IP DATABASE…"}
            {gpsBusy && "◌  ACQUIRING GPS SIGNAL…"}
          </div>
        )}

        {/* Two column: IP vs GPS */}
        <div style={{ ...S.cols }} className="two-col">

          {/* LEFT — IP Geolocation */}
          {ipData && (
            <Card title="IP Geolocation  (approximate)">
              <F label="IP Address" big value={ipData.query} />
              <div style={{ height: 14 }} />
              <div style={S.g2}>
                <F label="Country"  value={ipData.country} />
                <F label="Region"   value={ipData.region} />
                <F label="City"     value={ipData.city} />
                <F label="Postal"   value={ipData.zip} />
                <F label="Lat"      value={String(ipData.lat)} />
                <F label="Lon"      value={String(ipData.lon)} />
                <F label="ISP"      value={ipData.isp} />
                <F label="ASN"      value={ipData.as} />
                <F label="Timezone" value={ipData.timezone} />
              </div>
              <div style={S.mapBox}>
                <div style={{ fontSize: 22 }}>◎</div>
                <div style={{ color: "#00ff4166", fontSize: 12, letterSpacing: 1 }}>
                  {ipData.lat}° N · {ipData.lon}° E
                </div>
                <div style={{ fontSize: 10, letterSpacing: 2, color: "#00ff4133" }}>
                  {ipData.city}, {ipData.country}
                </div>
                <a
                  href={mapsLink(ipData.lat, ipData.lon)}
                  target="_blank"
                  rel="noreferrer"
                  style={{ fontSize: 10, color: "#00ff4155", letterSpacing: 2, marginTop: 4, textDecoration: "none" }}
                >
                  OPEN IN MAPS ↗
                </a>
              </div>
            </Card>
          )}

          {/* RIGHT — Real GPS Location */}
          {gpsData ? (
            <Card title="Real GPS Location  (accurate)" blue>
              <F label="Accuracy" big blue>
                <span style={S.bigBlue}>±{gpsData.accuracy}m</span>
              </F>
              <div style={{ height: 14 }} />
              <div style={S.g2}>
                <F blue label="Country"  value={gpsData.country} />
                <F blue label="State"    value={gpsData.state} />
                <F blue label="City"     value={gpsData.city} />
                <F blue label="Postcode" value={gpsData.postcode} />
                <F blue label="Latitude" value={gpsData.lat} />
                <F blue label="Longitude" value={gpsData.lon} />
              </div>
              <div style={S.mapBoxBlue}>
                <div style={{ fontSize: 22 }}>◉</div>
                <div style={{ color: "#00ccff77", fontSize: 12, letterSpacing: 1 }}>
                  {gpsData.lat}° N · {gpsData.lon}° E
                </div>
                <div style={{ fontSize: 9, letterSpacing: 1, color: "#00ccff44", textAlign: "center", maxWidth: 200 }}>
                  {gpsData.display.slice(0, 80)}…
                </div>
                <a
                  href={mapsLink(gpsData.lat, gpsData.lon)}
                  target="_blank"
                  rel="noreferrer"
                  style={{ fontSize: 10, color: "#00ccff55", letterSpacing: 2, marginTop: 4, textDecoration: "none" }}
                >
                  OPEN IN MAPS ↗
                </a>
              </div>
            </Card>
          ) : (
            <Card title="Real GPS Location" blue style={{ display: "flex", flexDirection: "column", alignItems: "center", justifyContent: "center", minHeight: 200 }}>
              <div style={{ fontSize: 32, marginBottom: 14 }}>◉</div>
              <div style={{ color: "#00ccff44", fontSize: 11, letterSpacing: 3, textAlign: "center", lineHeight: 2 }}>
                TAP THE BUTTON ABOVE<br />TO GET YOUR REAL LOCATION<br />
                <span style={{ fontSize: 10, color: "#00ccff22" }}>Uses GPS · Cell Towers · WiFi</span>
              </div>
            </Card>
          )}
        </div>

        {/* Comparison table — only when both are available */}
        {ipData && gpsData && (
          <div style={S.cmpCard} className="fade">
            <div style={S.cmpTitle}>⚖ IP vs GPS — Comparison</div>

            {[
              ["City",    ipData.city,            gpsData.city],
              ["State",   ipData.region,           gpsData.state],
              ["Country", ipData.country,          gpsData.country],
              ["Lat",     String(ipData.lat),      gpsData.lat],
              ["Lon",     String(ipData.lon),      gpsData.lon],
            ].map(([label, ipVal, gpsVal]) => (
              <div key={label} style={S.cmpRow}>
                <span style={S.cmpLabel}>{label}</span>
                <span style={{ ...S.cmpIp, fontSize: 11 }}>{ipVal}</span>
                <span style={{ color: "#ffffff22", fontSize: 10 }}>vs</span>
                <span style={{ ...S.cmpGps, fontSize: 11 }}>{gpsVal}</span>
                <span style={ipVal === gpsVal ? S.bdgGood : S.bdgWarn}>
                  {ipVal === gpsVal ? "✓ match" : "≠ differs"}
                </span>
              </div>
            ))}

            <div style={{ marginTop: 14, fontSize: 10, color: "#ffffff22", letterSpacing: 1, lineHeight: 1.8 }}>
              <span style={{ color: "#00ff4144" }}>◎ GREEN</span> = IP geolocation &nbsp;|&nbsp;
              <span style={{ color: "#00ccff44" 
