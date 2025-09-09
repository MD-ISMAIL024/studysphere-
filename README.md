import React, { useState, useEffect, useRef, useMemo, useCallback } from 'react';

export default function StudyAppFinal() {
  const [user, setUser] = useState(null);
  const [profile, setProfile] = useState(null);
  const [route, setRoute] = useState('signin');
  const [theme, setTheme] = useState('light');

  useEffect(() => { document.documentElement.setAttribute('data-theme', theme); }, [theme]);

  return (
    <div className={`${theme === 'dark' ? 'bg-slate-900 text-slate-100' : 'bg-gray-50 text-slate-900'} min-h-screen`}> 
      <Header user={user} onNavigate={setRoute} onToggleTheme={() => setTheme(t => t === 'dark' ? 'light' : 'dark')} onSignOut={() => { setUser(null); setProfile(null); setRoute('signin'); }} />
      <main className="max-w-6xl mx-auto p-6">
        {!user && route === 'signin' && <SignIn onSignIn={u => { setUser(u); setRoute('profile'); }} onCreate={() => setRoute('create')} />}
        {!user && route === 'create' && <CreateAccount onCreate={u => { setUser(u); setRoute('profile'); }} onBack={() => setRoute('signin')} />}
        {user && !profile && route === 'profile' && <StudentProfile defaultEmail={user.email} onSave={p => { setProfile(p); setRoute('dashboard'); }} />}

        {user && profile && route === 'dashboard' && <Dashboard profile={profile} onNavigate={setRoute} />}

        {user && profile && route === 'graphics' && <GraphicsWorkspace onBack={() => setRoute('dashboard')} />}
        {user && profile && route === 'templates' && <TemplatesGallery onBack={() => setRoute('dashboard')} />}
        {user && profile && route === 'video' && <VideoManager onBack={() => setRoute('dashboard')} />}
        {user && profile && route === 'whiteboard' && <AdvancedWhiteboard onBack={() => setRoute('dashboard')} />}
        {user && profile && route === 'assistant' && <AIAssistant onBack={() => setRoute('dashboard')} />}

      </main>
      <Footer />
    </div>
  );
}

function Header({ user, onNavigate, onToggleTheme, onSignOut }) {
  return (
    <header className="bg-gradient-to-r from-indigo-600 via-violet-600 to-pink-600 text-white p-4 shadow-md">
      <div className="max-w-6xl mx-auto flex items-center justify-between">
        <div className="flex items-center gap-4">
          <div className="text-2xl font-bold">StudySphere+</div>
          <nav className="hidden md:flex gap-2">
            <button onClick={() => onNavigate('dashboard')} className="px-3 py-1 rounded-md bg-white/20">Dashboard</button>
            <button onClick={() => onNavigate('graphics')} className="px-3 py-1 rounded-md bg-white/10">Graphics</button>
            <button onClick={() => onNavigate('templates')} className="px-3 py-1 rounded-md bg-white/10">Templates</button>
            <button onClick={() => onNavigate('video')} className="px-3 py-1 rounded-md bg-white/10">Videos</button>
            <button onClick={() => onNavigate('whiteboard')} className="px-3 py-1 rounded-md bg-white/10">Whiteboard</button>
          </nav>
        </div>
        <div className="flex items-center gap-3">
          <button onClick={onToggleTheme} className="px-3 py-1 rounded bg-white/20">Theme</button>
          {user ? (
            <div className="flex items-center gap-3">
              <div className="text-sm">{user.name}</div>
              <button onClick={onSignOut} className="px-3 py-1 rounded bg-white/20">Sign out</button>
            </div>
          ) : (
            <div className="flex gap-2">
              <button onClick={() => onNavigate('signin')} className="px-3 py-1 rounded bg-white/20">Sign in</button>
              <button onClick={() => onNavigate('create')} className="px-3 py-1 rounded bg-white/20">Create</button>
            </div>
          )}
        </div>
      </div>
    </header>
  );
}

function Footer() {
  return (
    <footer className="text-center p-4 text-sm text-slate-500">
      © {new Date().getFullYear()} StudySphere+. All rights reserved.
    </footer>
  );
}

function SignIn({ onSignIn, onCreate }) {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  return (
    <div className="max-w-md mx-auto bg-white p-6 rounded-lg shadow">
      <h2 className="text-xl font-semibold mb-4">Sign in</h2>
      <input className="w-full p-2 border rounded mb-2" placeholder="Name" value={name} onChange={e => setName(e.target.value)} />
      <input className="w-full p-2 border rounded mb-4" placeholder="Email" value={email} onChange={e => setEmail(e.target.value)} />
      <div className="flex gap-2">
        <button onClick={() => onSignIn({ id: Date.now(), name: name || 'Student', email: email || 'student@example.com' })} className="px-4 py-2 bg-indigo-600 text-white rounded">Sign in</button>
        <button onClick={onCreate} className="px-4 py-2 border rounded">Create account</button>
      </div>
    </div>
  );
}

function CreateAccount({ onCreate, onBack }) {
  const [form, setForm] = useState({ name: '', email: '', class: '', roll: '' });
  return (
    <div className="max-w-lg mx-auto bg-white p-6 rounded shadow">
      <h2 className="text-xl font-semibold mb-4">Create Account</h2>
      <div className="grid grid-cols-1 gap-2">
        <input placeholder="Name" value={form.name} onChange={e => setForm({ ...form, name: e.target.value })} className="p-2 border rounded" />
        <input placeholder="Email" value={form.email} onChange={e => setForm({ ...form, email: e.target.value })} className="p-2 border rounded" />
        <input placeholder="Class" value={form.class} onChange={e => setForm({ ...form, class: e.target.value })} className="p-2 border rounded" />
        <input placeholder="Roll" value={form.roll} onChange={e => setForm({ ...form, roll: e.target.value })} className="p-2 border rounded" />
      </div>
      <div className="mt-4 flex gap-2">
        <button onClick={() => onCreate({ id: Date.now(), name: form.name || 'Student', email: form.email || 'student@example.com' })} className="px-4 py-2 bg-green-600 text-white rounded">Create</button>
        <button onClick={onBack} className="px-4 py-2 border rounded">Back</button>
      </div>
    </div>
  );
}

function StudentProfile({ onSave, defaultEmail }) {
  const [profile, setProfile] = useState({ name: '', email: defaultEmail || '', class: '', roll: '' });
  return (
    <div className="max-w-2xl mx-auto bg-white p-6 rounded shadow">
      <h2 className="text-xl font-semibold mb-4">Student Profile</h2>
      <div className="grid grid-cols-1 md:grid-cols-2 gap-3">
        <input placeholder="Name" value={profile.name} onChange={e => setProfile({ ...profile, name: e.target.value })} className="p-2 border rounded" />
        <input placeholder="Email" value={profile.email} onChange={e => setProfile({ ...profile, email: e.target.value })} className="p-2 border rounded" />
        <input placeholder="Class" value={profile.class} onChange={e => setProfile({ ...profile, class: e.target.value })} className="p-2 border rounded" />
        <input placeholder="Roll" value={profile.roll} onChange={e => setProfile({ ...profile, roll: e.target.value })} className="p-2 border rounded" />
      </div>
      <div className="mt-4 flex gap-2">
        <button onClick={() => onSave(profile)} className="px-4 py-2 bg-indigo-600 text-white rounded">Save Profile</button>
      </div>
    </div>
  );
}

function Dashboard({ profile, onNavigate }) {
  const progress = useMemo(() => ({ physics: 55, chemistry: 40, biology: 68 }), []);
  return (
    <div className="grid grid-cols-1 lg:grid-cols-3 gap-4">
      <div className="col-span-2 bg-white p-4 rounded shadow">
        <div className="flex items-center justify-between">
          <div>
            <h3 className="text-xl font-semibold">Welcome, {profile.name || 'Student'}</h3>
            <p className="text-sm text-slate-500">Your personalized study dashboard — smart suggestions, fast access.</p>
          </div>
          <div className="text-right">
            <div className="text-sm">Class: {profile.class || '-'}</div>
            <div className="text-sm">Roll: {profile.roll || '-'}</div>
          </div>
        </div>

        <div className="mt-4 grid grid-cols-1 md:grid-cols-3 gap-3">
          <TrackCard subject="Physics" percent={progress.physics} />
          <TrackCard subject="Chemistry" percent={progress.chemistry} />
          <TrackCard subject="Biology" percent={progress.biology} />
        </div>

        <div className="mt-6 flex flex-wrap gap-3">
          <button onClick={() => onNavigate('whiteboard')} className="px-4 py-2 rounded bg-indigo-600 text-white">Whiteboard</button>
          <button onClick={() => onNavigate('graphics')} className="px-4 py-2 rounded bg-purple-600 text-white">Graphics</button>
          <button onClick={() => onNavigate('templates')} className="px-4 py-2 rounded bg-green-600 text-white">Templates</button>
          <button onClick={() => onNavigate('video')} className="px-4 py-2 rounded bg-red-600 text-white">Video Playlists</button>
          <button onClick={() => onNavigate('assistant')} className="px-4 py-2 rounded bg-slate-700 text-white">AI Assistant</button>
        </div>
      </div>

      <div className="bg-white p-4 rounded shadow">
        <h4 className="font-semibold">Quick Tools</h4>
        <ul className="mt-3 space-y-2">
          <li><button onClick={() => onNavigate('templates')} className="w-full text-left px-3 py-2 border rounded">Templates</button></li>
          <li><button onClick={() => onNavigate('graphics')} className="w-full text-left px-3 py-2 border rounded">Graphics Library</button></li>
          <li><button onClick={() => onNavigate('video')} className="w-full text-left px-3 py-2 border rounded">YouTube Playlists</button></li>
        </ul>
        <div className="mt-4">
          <NotesSide />
        </div>
      </div>
    </div>
  );
}

function NotesSide() {
  const [notes, setNotes] = useState(() => { try { return JSON.parse(localStorage.getItem('notes') || '[]'); } catch { return []; } });
  const [text, setText] = useState('');
  useEffect(() => { localStorage.setItem('notes', JSON.stringify(notes)); }, [notes]);
  return (
    <div>
      <div className="flex items-center justify-between mb-2">
        <div className="font-medium">Quick Notes</div>
        <button onClick={() => { setNotes([]); setText(''); }} className="text-xs text-red-500">Clear</button>
      </div>
      <textarea value={text} onChange={e => setText(e.target.value)} placeholder="Write a quick note..." className="w-full p-2 border rounded mb-2" />
      <div className="flex gap-2">
        <button onClick={() => { if (text.trim()) { setNotes(n => [{ id: Date.now(), text }, ...n]); setText(''); } }} className="px-3 py-1 bg-indigo-600 text-white rounded">Add</button>
        <button onClick={() => { navigator.clipboard?.writeText(notes.map(n=>n.text).join('

')); }} className="px-3 py-1 border rounded">Copy All</button>
      </div>
      <div className="mt-3 space-y-2 max-h-40 overflow-auto">
        {notes.map(n => <div key={n.id} className="p-2 border rounded bg-white">{n.text}</div>)}
      </div>
    </div>
  );
}

// ---------------- Graphics Manager ----------------
function GraphicsWorkspace({ onBack }) {
  return (
    <div className="bg-white p-4 rounded shadow">
      <div className="flex items-center justify-between mb-4">
        <h3 className="text-lg font-semibold">Graphics Library</h3>
        <div className="flex gap-2">
          <button onClick={onBack} className="px-3 py-1 border rounded">Back</button>
        </div>
      </div>
      <GraphicsManager />
    </div>
  );
}

function GraphicsManager() {
  const [items, setItems] = useState([]);
  const [loading, setLoading] = useState(true);
  const [q, setQ] = useState('');
  const [subject, setSubject] = useState('All');
  const [limit, setLimit] = useState(100);

  useEffect(() => {
    let mounted = true;
    fetch('/graphics.json').then(r => r.json()).then(d => { if (mounted) setItems(d); }).catch(() => { if (mounted) setItems([]); }).finally(() => { if (mounted) setLoading(false); });
    return () => { mounted = false; };
  }, []);

  const subjects = useMemo(() => ['All', ...Array.from(new Set(items.map(i => i.subject)))], [items]);
  const filtered = useMemo(() => {
    const qq = q.trim().toLowerCase();
    return items.filter(it => {
      if (subject !== 'All' && it.subject !== subject) return false;
      if (!qq) return true;
      return (it.title + ' ' + (it.tags||[]).join(' ')).toLowerCase().includes(qq);
    });
  }, [items, q, subject]);

  return (
    <div>
      <div className="flex gap-2 mb-4">
        <input placeholder="Search visuals" value={q} onChange={e=>setQ(e.target.value)} className="p-2 border rounded flex-1" />
        <select value={subject} onChange={e=>setSubject(e.target.value)} className="p-2 border rounded"><option>All</option>{subjects.filter(s=>s!=='All').map(s=> <option key={s}>{s}</option>)}</select>
        <select value={limit} onChange={e=>setLimit(Number(e.target.value))} className="p-2 border rounded">{[50,100,200,500,1000].map(n=> <option key={n} value={n}>{n}</option>)}</select>
      </div>
      {loading ? <div>Loading visuals…</div> : (
        <div style={{display:'grid', gridTemplateColumns:'repeat(auto-fill,minmax(200px,1fr))', gap:12}}>
          {filtered.slice(0,limit).map(it => (
            <figure key={it.id} className="border rounded p-2 bg-white">
              <div style={{aspectRatio:'16/10', overflow:'hidden', borderRadius:8, background:'#f8fafc'}}>
                <img src={it.image} alt={it.title} loading="lazy" style={{width:'100%',height:'100%',objectFit:'cover'}} />
              </div>
              <figcaption className="mt-2">
                <div className="font-semibold">{it.title}</div>
                <div className="text-xs text-slate-500">{it.subject}</div>
              </figcaption>
            </figure>
          ))}
        </div>
      )}
    </div>
  );
}

// ---------------- Templates ----------------
function TemplatesGallery({ onBack }) {
  const [templates, setTemplates] = useState([]);
  const [loading, setLoading] = useState(true);
  useEffect(()=>{ let m=true; fetch('/templates.json').then(r=>r.json()).then(d=>{ if(m) setTemplates(d); }).catch(()=>{ if(m) setTemplates([]); }).finally(()=>{ if(m) setLoading(false); }); return ()=> m=false; },[]);
  return (
    <div className="bg-white p-4 rounded shadow">
      <div className="flex items-center justify-between mb-4">
        <h3 className="text-lg font-semibold">Templates</h3>
        <div className="flex gap-2"><button onClick={onBack} className="px-3 py-1 border rounded">Back</button></div>
      </div>
      {loading ? <div>Loading templates…</div> : (
        <div style={{display:'grid',gridTemplateColumns:'repeat(auto-fill,minmax(220px,1fr))',gap:12}}>
          {templates.map(t=> (
            <div key={t.id} className="border rounded p-3 bg-white">
              <div className="font-semibold">{t.title}</div>
              <div className="text-xs text-slate-500">{t.subject}</div>
              <div className="mt-2 text-sm">{t.description}</div>
            </div>
          ))}
        </div>
      )}
    </div>
  );
}

// ---------------- Video Manager ----------------
function VideoManager({ onBack }) {
  return (
    <div className="bg-white p-4 rounded shadow">
      <div className="flex items-center justify-between mb-4">
        <h3 className="text-lg font-semibold">Video Playlists</h3>
        <div className="flex gap-2"><button onClick={onBack} className="px-3 py-1 border rounded">Back</button></div>
      </div>
      <VideoPlaylistUI />
    </div>
  );
}

function VideoPlaylistUI(){
  const [apiKey, setApiKey] = useState('');
  const [playlistId, setPlaylistId] = useState('');
  const [videos, setVideos] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  const load = useCallback(async () => {
    setError(null); setLoading(true); setVideos([]);
    try{
      const url = `https://www.googleapis.com/youtube/v3/playlistItems?part=snippet&maxResults=50&playlistId=${encodeURIComponent(playlistId)}&key=${encodeURIComponent(apiKey)}`;
      const res = await fetch(url);
      const d = await res.json();
      if(d.error) throw new Error(d.error.message || 'YouTube error');
      const items = (d.items||[]).filter(i=>i.snippet && i.snippet.resourceId).map(i=>({ id:i.id, title:i.snippet.title, videoId:i.snippet.resourceId.videoId }));
      setVideos(items);
    }catch(e){ setError(String(e)); }
    finally{ setLoading(false); }
  }, [apiKey, playlistId]);

  return (
    <div>
      <div className="grid grid-cols-1 md:grid-cols-3 gap-2 mb-4">
        <input placeholder="YouTube API Key" value={apiKey} onChange={e=>setApiKey(e.target.value)} className="p-2 border rounded" />
        <input placeholder="Playlist ID or full playlist URL" value={playlistId} onChange={e=>setPlaylistId(e.target.value)} className="p-2 border rounded" />
        <div className="flex gap-2">
          <button onClick={load} className="px-4 py-2 bg-indigo-600 text-white rounded">Load</button>
          <button onClick={()=>{ setApiKey(''); setPlaylistId(''); setVideos([]); }} className="px-4 py-2 border rounded">Clear</button>
        </div>
      </div>

      {loading && <div>Loading videos…</div>}
      {error && <div className="text-red-600">{error}</div>}

      <div style={{display:'grid',gridTemplateColumns:'repeat(auto-fill,minmax(320px,1fr))',gap:12}}>
        {videos.map(v=> (
          <div key={v.id} className="border rounded overflow-hidden bg-black">
            <iframe title={v.title} width="100%" height="200" src={`https://www.youtube.com/embed/${v.videoId}`} allowFullScreen />
            <div style={{padding:8, background:'#fff'}}>{v.title}</div>
          </div>
        ))}
      </div>
    </div>
  );
}

// ---------------- Advanced Whiteboard (BoardMix-like basic tools) ----------------
function AdvancedWhiteboard({ onBack }){
  const canvasRef = useRef(null);
  const containerRef = useRef(null);
  const [tool, setTool] = useState('pen');
  const [penSize, setPenSize] = useState(3);
  const [color, setColor] = useState('#111827');
  const [shapes, setShapes] = useState([]); // vector shapes
  const [mode, setMode] = useState('draw'); // draw | shape | text | select
  const [selectedShape, setSelectedShape] = useState(null);

  useEffect(()=>{
    const canvas = canvasRef.current; const container = containerRef.current; if(!canvas||!container) return; const ctx = canvas.getContext('2d');
    function resize(){ const rect = container.getBoundingClientRect(); const ratio = window.devicePixelRatio||1; canvas.width = Math.floor(rect.width*ratio); canvas.height = Math.floor(rect.height*ratio); canvas.style.width = `${rect.width}px`; canvas.style.height = `${rect.height}px`; ctx.setTransform(ratio,0,0,ratio,0,0); ctx.fillStyle='#fff'; ctx.fillRect(0,0,rect.width,rect.height); redrawShapes(); }
    function redrawShapes(){ const rect = container.getBoundingClientRect(); ctx.clearRect(0,0,rect.width,rect.height); ctx.fillStyle='#fff'; ctx.fillRect(0,0,rect.width,rect.height); // draw shapes
      shapes.forEach(s=>{ if(s.type==='rect'){ ctx.fillStyle=s.fill||'transparent'; ctx.strokeStyle=s.stroke||'#000'; ctx.lineWidth=s.strokeWidth||2; ctx.fillRect(s.x,s.y,s.w,s.h); ctx.strokeRect(s.x,s.y,s.w,s.h); } else if(s.type==='text'){ ctx.fillStyle=s.color||'#000'; ctx.font=`${s.size||16}px sans-serif`; ctx.fillText(s.text,s.x,s.y); } }); }
    resize(); window.addEventListener('resize', resize); return ()=> window.removeEventListener('resize', resize);
  }, [shapes]);

  useEffect(()=>{
    const canvas = canvasRef.current; if(!canvas) return; const ctx = canvas.getContext('2d'); let drawing=false; let last=null;
    const getPos = e=>{ const rect=canvas.getBoundingClientRect(); if(e.touches && e.touches[0]) return {x:e.touches[0].clientX-rect.left,y:e.touches[0].clientY-rect.top}; return {x:e.clientX-rect.left,y:e.clientY-rect.top}; };
    const down = e=>{ if(mode==='draw'){ drawing=true; last=getPos(e); ctx.beginPath(); ctx.moveTo(last.x,last.y); } else if(mode==='shape'){ const p=getPos(e); setShapes(s=>[...s,{ id:Date.now(), type:'rect', x:p.x, y:p.y, w:0, h:0, stroke:color, strokeWidth:penSize, fill:'transparent' }]); setSelectedShape(null); }
      else if(mode==='text'){ const p=getPos(e); const txt = prompt('Enter text'); if(txt){ setShapes(s=>[...s,{ id:Date.now(), type:'text', x:p.x, y:p.y, text:txt, color:color, size:18 }]); } }
    };
    const move = e=>{ if(mode==='draw' && drawing){ const p=getPos(e); ctx.lineTo(p.x,p.y); ctx.strokeStyle=color; ctx.lineWidth=penSize; ctx.stroke(); last=p; }
      if(mode==='shape'){ // update last shape
        setShapes(s=>{ const copy=[...s]; const lastIdx=copy.length-1; if(lastIdx>=0){ const item=copy[lastIdx]; const p=getPos(e); item.w = p.x - item.x; item.h = p.y - item.y; copy[lastIdx]=item; } return copy; }); }
    };
    const up = e=>{ drawing=false; last=null; };
    canvas.addEventListener('mousedown', down); canvas.addEventListener('mousemove', move); window.addEventListener('mouseup', up);
    canvas.addEventListener('touchstart', down, { passive:false }); canvas.addEventListener('touchmove', move, { passive:false }); window.addEventListener('touchend', up);
    return ()=>{ canvas.removeEventListener('mousedown', down); canvas.removeEventListener('mousemove', move); window.removeEventListener('mouseup', up); canvas.removeEventListener('touchstart', down); canvas.removeEventListener('touchmove', move); window.removeEventListener('touchend', up); };
  }, [mode, color, penSize]);

  const clearBoard = ()=>{ const canvas=canvasRef.current; if(!canvas) return; const ctx=canvas.getContext('2d'); const rect=canvas.getBoundingClientRect(); ctx.clearRect(0,0,rect.width,rect.height); ctx.fillStyle='#fff'; ctx.fillRect(0,0,rect.width,rect.height); setShapes([]); }

  return (
    <div className="bg-white p-4 rounded shadow">
      <div className="flex items-center justify-between mb-3">
        <h3 className="text-lg font-semibold">Advanced Whiteboard</h3>
        <div className="flex items-center gap-2">
          <select value={mode} onChange={e=>setMode(e.target.value)} className="p-2 border rounded">
            <option value="draw">Draw</option>
            <option value="shape">Shape</option>
            <option value="text">Text</option>
            <option value="select">Select</option>
          </select>
          <input type="range" min={1} max={24} value={penSize} onChange={e=>setPenSize(Number(e.target.value))} />
          <input type="color" value={color} onChange={e=>setColor(e.target.value)} />
          <button onClick={clearBoard} className="px-3 py-1 border rounded">Clear</button>
          <button onClick={onBack} className="px-3 py-1 border rounded">Back</button>
        </div>
      </div>
      <div ref={containerRef} style={{height:520}} className="border rounded overflow-hidden">
        <canvas ref={canvasRef} className="w-full h-full block" />
      </div>
      <div className="mt-3">
        <div className="font-semibold mb-2">Layers / Objects</div>
        <div className="space-y-2 max-h-36 overflow-auto">
          {shapes.map(s=> (
            <div key={s.id} className="flex items-center justify-between p-2 border rounded bg-white">
              <div className="text-sm">{s.type} {s.text? `: ${s.text}`:''}</div>
              <div className="flex gap-2">
                <button onClick={()=> setShapes(prev => prev.filter(x=>x.id !== s.id))} className="text-xs text-red-500">Delete</button>
              </div>
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}

// ---------------- AI Assistant ----------------
function AIAssistant({ onBack }){
  const [messages, setMessages] = useState([{ id:1, role:'assistant', text:'I am your AI assistant. Ask me study questions.' }]);
  const [input, setInput] = useState('');
  const [loading, setLoading] = useState(false);

  const send = useCallback(async ()=>{
    if(!input.trim()) return; const userMsg={ id:Date.now(), role:'user', text:input }; setMessages(m=>[...m,userMsg]); setInput(''); setLoading(true);
    try{
      const res = await fetch('/api/assistant',{ method:'POST', headers:{'Content-Type':'application/json'}, body: JSON.stringify({ message: userMsg.text }) });
      const data = await res.json(); setMessages(m=>[...m,{ id:Date.now()+1, role:'assistant', text: data.reply || 'No reply' }]);
    }catch(e){ setMessages(m=>[...m,{ id:Date.now()+1, role:'assistant', text: `Error: ${String(e)}` }]); }
    finally{ setLoading(false); }
  }, [input]);

  return (
    <div className="bg-white p-4 rounded shadow max-w-3xl mx-auto">
      <div className="flex items-center justify-between mb-3">
        <h3 className="font-semibold">AI Study Assistant</h3>
        <div className="flex gap-2"><button onClick={onBack} className="px-3 py-1 border rounded">Back</button></div>
      </div>
      <div className="border rounded p-3 h-80 overflow-auto bg-slate-50">
        {messages.map(m=> <div key={m.id} className={`my-2 ${m.role==='assistant' ? 'text-left':'text-right'}`}><div className={`inline-block p-2 rounded ${m.role==='assistant' ? 'bg-white border' : 'bg-indigo-600 text-white'}`}>{m.text}</div></div>)}
      </div>
      <div className="mt-3 flex gap-2">
        <input value={input} onChange={e=>setInput(e.target.value)} placeholder="Ask a question..." className="flex-1 p-2 border rounded" />
        <button onClick={send} disabled={loading} className="px-4 py-2 bg-indigo-600 text-white rounded">{loading? 'Thinking...':'Send'}</button>
      </div>
    </div>
  );
}

// ---------------- End ----------------
