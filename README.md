
============================================================
// MoneyGrow - 家計簿・資産管理アプリ
// ============================================================
// 構成:
// 1. DESIGN SYSTEM (色・グロー・定数)
// 2. ICON SYSTEM (SVGアイコン・IconBox)
// 3. SHARED DATA (カテゴリ・初期データ・ユーティリティ)
// 4. UI PRIMITIVES (Card・NBtn・STitle・NeonTooltip)
// 5. COMPONENTS (BottomNav・TxRow・TxModal)
// 6. TAB SCREENS (Home・Kakeibo・Assets・Subs・Reports・Future・Tax)
// 7. APP ROOT (App)
// ============================================================

import { useState } from "react";
import {
AreaChart, Area,
BarChart, Bar,
PieChart, Pie, Cell,
XAxis, YAxis, CartesianGrid, Tooltip,
Legend, ResponsiveContainer,
} from "recharts";

// ============================================================
// 1. DESIGN SYSTEM
// ============================================================

const C = {
// Backgrounds
bg: "#0B0F1A",
card: "#111827",
card2: "#161D2E",
input: "#1A2535",
border: "rgba(255,255,255,0.07)",
// Neon Colors
cyan: "#00F5FF",
green: "#00FF88",
purple: "#A855F7",
orange: "#FF8C00",
pink: "#FF3CAC",
yellow: "#FFD700",
blue: "#3B82F6",
// Text
text: "#F0F6FF",
textSub: "#6B85A3",
textDim: "#3D5068",
};

const glow = (color, px = 8) => `0 0 ${px}px ${color}66, 0 0 ${px * 2}px ${color}33`;
const glowStrong = (color) => `0 0 12px ${color}99, 0 0 24px ${color}55, 0 0 40px ${color}22`;
const alpha = (color, a) => color + Math.round(a * 255).toString(16).padStart(2, "0");

// ============================================================
// 2. ICON SYSTEM
// ============================================================

/**
* SVGアイコンコンポーネント。
* nameに対応するアイコンを描画します。
* 追加する場合はiconsオブジェクトにキーと<svg>を追加してください。
*/
const Icon = ({ name, size = 20, color = C.cyan }) => {
const svgProps = {
viewBox: "0 0 24 24",
fill: "none",
stroke: color,
strokeWidth: 1.6,
strokeLinecap: "round",
strokeLinejoin: "round",
style: { filter: `drop-shadow(0 0 4px ${color}88)` },
};

const icons = {
home: <svg {...svgProps}><path d="M3 9.5L12 3l9 6.5V20a1 1 0 01-1 1H4a1 1 0 01-1-1V9.5z"/><path d="M9 21V12h6v9"/></svg>,
book: <svg {...svgProps}><path d="M4 19.5A2.5 2.5 0 016.5 17H20"/><path d="M6.5 2H20v20H6.5A2.5 2.5 0 014 19.5v-15A2.5 2.5 0 016.5 2z"/></svg>,
wallet: <svg {...svgProps}><rect x="2" y="5" width="20" height="14" rx="2"/><circle cx="16" cy="12" r="1" fill={color}/><path d="M2 10h20"/></svg>,
repeat: <svg {...svgProps}><polyline points="17 1 21 5 17 9"/><path d="M3 11V9a4 4 0 014-4h14"/><polyline points="7 23 3 19 7 15"/><path d="M21 13v2a4 4 0 01-4 4H3"/></svg>,
chart: <svg {...svgProps}><line x1="18" y1="20" x2="18" y2="10"/><line x1="12" y1="20" x2="12" y2="4"/><line x1="6" y1="20" x2="6" y2="14"/></svg>,
star: <svg {...svgProps}><polygon points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2"/></svg>,
bulb: <svg {...svgProps}><line x1="9" y1="18" x2="15" y2="18"/><line x1="10" y1="22" x2="14" y2="22"/><path d="M12 2a7 7 0 017 7c0 2.38-1.19 4.47-3 5.74V17H8v-2.26C6.19 13.47 5 11.38 5 9a7 7 0 017-7z"/></svg>,
plus: <svg {...svgProps}><line x1="12" y1="5" x2="12" y2="19"/><line x1="5" y1="12" x2="19" y2="12"/></svg>,
close: <svg {...svgProps}><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>,
bell: <svg {...svgProps}><path d="M18 8A6 6 0 006 8c0 7-3 9-3 9h18s-3-2-3-9"/><path d="M13.73 21a2 2 0 01-3.46 0"/></svg>,
arrowUp: <svg {...svgProps}><line x1="12" y1="19" x2="12" y2="5"/><polyline points="5 12 12 5 19 12"/></svg>,
arrowDn: <svg {...svgProps}><line x1="12" y1="5" x2="12" y2="19"/><polyline points="19 12 12 19 5 12"/></svg>,
transfer: <svg {...svgProps}><polyline points="17 1 21 5 17 9"/><polyline points="7 23 3 19 7 15"/><line x1="3" y1="5" x2="21" y2="5"/><line x1="3" y1="19" x2="21" y2="19"/></svg>,
lock: <svg {...svgProps}><rect x="3" y="11" width="18" height="11" rx="2"/><path d="M7 11V7a5 5 0 0110 0v4"/></svg>,
dollar: <svg {...svgProps}><line x1="12" y1="1" x2="12" y2="23"/><path d="M17 5H9.5a3.5 3.5 0 000 7h5a3.5 3.5 0 010 7H6"/></svg>,
trend: <svg {...svgProps}><polyline points="23 6 13.5 15.5 8.5 10.5 1 18"/><polyline points="17 6 23 6 23 12"/></svg>,
shield: <svg {...svgProps}><path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"/></svg>,
info: <svg {...svgProps}><circle cx="12" cy="12" r="10"/><line x1="12" y1="16" x2="12" y2="12"/><line x1="12" y1="8" x2="12.01" y2="8"/></svg>,
warning: <svg {...svgProps}><path d="M10.29 3.86L1.82 18a2 2 0 001.71 3h16.94a2 2 0 001.71-3L13.71 3.86a2 2 0 00-3.42 0z"/><line x1="12" y1="9" x2="12" y2="13"/><line x1="12" y1="17" x2="12.01" y2="17"/></svg>,
check: <svg {...svgProps}><polyline points="20 6 9 17 4 12"/></svg>,
tag: <svg {...svgProps}><path d="M20.59 13.41l-7.17 7.17a2 2 0 01-2.83 0L2 12V2h10l8.59 8.59a2 2 0 010 2.82z"/><line x1="7" y1="7" x2="7.01" y2="7"/></svg>,
pie: <svg {...svgProps}><path d="M21.21 15.89A10 10 0 118 2.83"/><path d="M22 12A10 10 0 0012 2v10z"/></svg>,
};

return (
<span style={{ width: size, height: size, display: "inline-flex", alignItems: "center", justifyContent: "center", flexShrink: 0 }}>
{icons[name] ?? icons.dollar}
</span>
);
};

/** カラー枠付きアイコンボックス */
const IconBox = ({ icon, color = C.cyan, size = 38 }) => (
<div style={{
width: size, height: size,
borderRadius: Math.round(size * 0.28),
flexShrink: 0,
background: `linear-gradient(135deg, ${alpha(color, 0.12)}, ${alpha(color, 0.05)})`,
border: `1px solid ${alpha(color, 0.4)}`,
display: "flex", alignItems: "center", justifyContent: "center",
boxShadow: glow(color, 5),
}}>
<Icon name={icon} size={Math.round(size * 0.5)} color={color} />
</div>
);

// ============================================================
// 3. SHARED DATA
// ============================================================

/** 数値フォーマット (例: 1234567 → "1,234,567") */
const fmt = (n) => new Intl.NumberFormat("ja-JP").format(Math.abs(Math.round(n)));
const today = () => new Date().toISOString().split("T")[0];

// カテゴリ定義 ─ アイコン絵文字・テーマカラーをここで一元管理
const EXPENSE_CATS = [
{ name: "食費", emoji: "🍜", color: C.orange },
{ name: "日用品", emoji: "🛒", color: C.yellow },
{ name: "光熱費", emoji: "⚡", color: C.cyan },
{ name: "通信費", emoji: "📡", color: C.blue },
{ name: "交通費", emoji: "🚃", color: C.green },
{ name: "医療費", emoji: "💊", color: C.pink },
{ name: "娯楽費", emoji: "🎮", color: C.purple },
{ name: "交際費", emoji: "🤝", color: C.cyan },
{ name: "保険", emoji: "🔒", color: C.green },
{ name: "その他", emoji: "💡", color: C.textSub},
];
const INCOME_CATS = [
{ name: "給与", emoji: "💰", color: C.green },
{ name: "賞与", emoji: "🎁", color: C.yellow },
{ name: "副業", emoji: "🌐", color: C.purple },
{ name: "配当金", emoji: "📈", color: C.orange },
{ name: "その他", emoji: "💡", color: C.textSub},
];

// カテゴリ名 → { emoji, color } のルックアップ
const CAT_MAP = Object.fromEntries(
[...EXPENSE_CATS, ...INCOME_CATS].map((c) => [c.name, c])
);
const catColor = (name) => CAT_MAP[name]?.color ?? C.textSub;

// 資産種別ラベル・アイコン
const ASSET_TYPE = {
cash: { label: "現金", icon: "dollar" },
bank: { label: "銀行口座", icon: "lock" },
securities: { label: "証券口座", icon: "trend" },
nisa: { label: "NISA", icon: "star" },
credit: { label: "クレジット",icon: "wallet" },
};
const ASSET_COLORS = [C.cyan, C.green, C.purple, C.orange, C.pink];
const PIE_COLORS = [C.cyan, C.green, C.purple, C.orange, C.pink, C.yellow, C.blue, "#FF6B6B", "#4ECDC4", "#95E1D3"];

// 初期データ
const INIT_TRANSACTIONS = [
{ id:1, type:"income", date:"2025-06-01", amount:320000, category:"給与", memo:"6月給与", tags:["固定"], fixed:true },
{ id:2, type:"expense", date:"2025-06-02", amount:12500, category:"食費", memo:"スーパー", tags:[], fixed:false },
{ id:3, type:"expense", date:"2025-06-03", amount:8800, category:"光熱費", memo:"電気代", tags:["固定費"],fixed:true },
{ id:4, type:"expense", date:"2025-06-05", amount:6500, category:"通信費", memo:"スマホ", tags:["固定費"],fixed:true },
{ id:5, type:"expense", date:"2025-06-08", amount:15000, category:"食費", memo:"外食", tags:[], fixed:false },
{ id:6, type:"expense", date:"2025-06-10", amount:3200, category:"交通費", memo:"定期", tags:["固定費"],fixed:true },
{ id:7, type:"expense", date:"2025-06-12", amount:45000, category:"娯楽費", memo:"旅行", tags:["旅行"], fixed:false },
{ id:8, type:"expense", date:"2025-06-15", amount:9800, category:"日用品", memo:"生活用品", tags:[], fixed:false },
{ id:9, type:"income", date:"2025-06-20", amount:50000, category:"副業", memo:"フリーランス",tags:["副業"], fixed:false },
{ id:10, type:"expense", date:"2025-06-22", amount:25000, category:"交際費", memo:"飲み会", tags:[], fixed:false },
];

const INIT_ASSETS = [
{ id:1, name:"財布（現金）", type:"cash", balance:45000 },
{ id:2, name:"三菱UFJ銀行", type:"bank", balance:1250000 },
{ id:3, name:"SBI証券", type:"securities", balance:850000 },
{ id:4, name:"つみたてNISA", type:"nisa", balance:320000 },
{ id:5, name:"楽天カード", type:"credit", balance:-45800 },
];

const INIT_SUBSCRIPTIONS = [
{ id:1, name:"Netflix", monthly:1490, next:"2025-07-01", method:"楽天カード" },
{ id:2, name:"Spotify", monthly:980, next:"2025-07-05", method:"楽天カード" },
{ id:3, name:"Adobe CC", monthly:6248, next:"2025-07-15", method:"楽天カード" },
{ id:4, name:"iCloud 200GB", monthly:400, next:"2025-07-10", method:"Apple Pay" },
];

const CHART_ASSET_HISTORY = [
{ m:"1月", v:2200000 }, { m:"2月", v:2285000 }, { m:"3月", v:2310000 },
{ m:"4月", v:2398000 }, { m:"5月", v:2420000 }, { m:"6月", v:2465000 },
];

const CHART_MONTHLY = [
{ m:"1月", inc:350000, exp:220000 }, { m:"2月", inc:320000, exp:195000 },
{ m:"3月", inc:320000, exp:250000 }, { m:"4月", inc:370000, exp:210000 },
{ m:"5月", inc:320000, exp:185000 }, { m:"6月", inc:370000, exp:125300 },
];

const BUDGETS = { 食費:30000, 光熱費:10000, 通信費:8000, 娯楽費:20000, 交際費:15000 };

// ============================================================
// 4. UI PRIMITIVES
// ============================================================

/** グラスモーフィズム風カード */
const Card = ({ children, glowColor = null, style = {}, onClick }) => (
<div
onClick={onClick}
style={{
background: C.card,
border: `1px solid ${glowColor ? alpha(glowColor, 0.35) : C.border}`,
borderRadius: 16,
padding: 16,
boxShadow: glowColor ? glow(glowColor, 10) : "0 2px 12px rgba(0,0,0,0.4)",
cursor: onClick ? "pointer" : "default",
...style,
}}
>
{children}
</div>
);

/** ネオンボタン */
const NBtn = ({ children, color = C.cyan, onClick, style = {}, size = "md" }) => {
const pad = size === "sm" ? "6px 12px" : size === "lg" ? "14px 24px" : "9px 16px";
const fs = size === "sm" ? 10 : size === "lg" ? 14 : 11;
return (
<button
onClick={onClick}
style={{
background: alpha(color, 0.15),
border: `1px solid ${alpha(color, 0.5)}`,
borderRadius: 99,
padding: pad,
color,
fontSize: fs,
fontWeight: 700,
cursor: "pointer",
fontFamily: "inherit",
boxShadow: glow(color, 5),
transition: "all 0.15s",
...style,
}}
>
{children}
</button>
);
};

/** セクションタイトル (左アクセントバー付き) */
const STitle = ({ children, color = C.cyan }) => (
<div style={{ display: "flex", alignItems: "center", gap: 8, marginBottom: 14 }}>
<div style={{ width: 3, height: 16, background: color, borderRadius: 99, boxShadow: glow(color) }} />
<span style={{ fontSize: 13, fontWeight: 700, color: C.text, letterSpacing: "0.03em" }}>{children}</span>
</div>
);

/** カスタムチャートTooltip */
const NeonTooltip = ({ active, payload, label }) => {
if (!active || !payload?.length) return null;
return (
<div style={{
background: C.card2,
border: `1px solid ${alpha(C.cyan, 0.35)}`,
borderRadius: 10,
padding: "8px 12px",
boxShadow: glow(C.cyan, 8),
}}>
{label && <div style={{ fontSize: 10, color: C.textSub, marginBottom: 4 }}>{label}</div>}
{payload.map((p, i) => (
<div key={i} style={{ fontSize: 11, color: p.color ?? C.text, fontWeight: 700 }}>
{p.name}: ¥{fmt(p.value)}
</div>
))}
</div>
);
};

/** テキスト入力フィールド */
const Field = ({ label, value, onChange, type = "text", step, placeholder, color = C.cyan }) => (
<div style={{ marginBottom: 12 }}>
<div style={{ fontSize: 10, color: C.textSub, marginBottom: 4 }}>{label}</div>
<input
type={type}
value={value}
step={step}
placeholder={placeholder}
onChange={(e) => onChange(e.target.value)}
style={{
width: "100%",
background: C.input,
border: `1px solid ${alpha(color, 0.3)}`,
borderRadius: 10,
padding: "10px 12px",
fontSize: type === "number" ? 14 : 12,
fontFamily: type === "number" ? "Orbitron, monospace" : "inherit",
color,
outline: "none",
}}
/>
</div>
);

/** ON/OFFトグル */
const Toggle = ({ checked, onChange, label }) => (
<label style={{ display: "flex", alignItems: "center", gap: 8, cursor: "pointer", marginBottom: 14 }}>
<div
onClick={() => onChange(!checked)}
style={{
width: 36, height: 20, borderRadius: 99,
background: checked ? C.cyan : C.input,
border: `1px solid ${checked ? C.cyan : C.border}`,
position: "relative",
transition: "all 0.2s",
boxShadow: checked ? glow(C.cyan, 5) : "none",
flexShrink: 0,
}}
>
<div style={{
position: "absolute",
top: 2, left: checked ? 18 : 2,
width: 16, height: 16,
borderRadius: 99,
background: checked ? "#fff" : C.textDim,
transition: "left 0.2s",
}} />
</div>
<span style={{ fontSize: 11, color: C.textSub }}>{label}</span>
</label>
);

// ============================================================
// 5. COMPONENTS
// ============================================================

/** 取引1行 */
const TxRow = ({ tx }) => {
const isIncome = tx.type === "income";
const color = isIncome ? C.green : catColor(tx.category);
return (
<div style={{
display: "flex", alignItems: "center", justifyContent: "space-between",
padding: "10px 0",
borderBottom: `1px solid ${C.border}`,
}}>
{/* 左: アイコン + テキスト */}
<div style={{ display: "flex", alignItems: "center", gap: 10 }}>
<IconBox icon={isIncome ? "arrowUp" : "arrowDn"} color={color} size={34} />
<div>
<div style={{ fontSize: 12, fontWeight: 600, color: C.text }}>{tx.category}</div>
<div style={{ fontSize: 9, color: C.textSub }}>
{tx.date}{tx.memo ? ` · ${tx.memo}` : ""}
</div>
{tx.tags.length > 0 && (
<div style={{ display: "flex", gap: 4, marginTop: 2 }}>
{tx.tags.map((tag) => (
<span key={tag} style={{
fontSize: 8, color: C.purple,
background: alpha(C.purple, 0.15),
border: `1px solid ${alpha(C.purple, 0.4)}`,
borderRadius: 99, padding: "1px 5px",
}}>{tag}</span>
))}
</div>
)}
</div>
</div>
{/* 右: 金額 + 固定/変動 */}
<div style={{ textAlign: "right" }}>
<div style={{ fontFamily: "Orbitron, monospace", fontWeight: 700, fontSize: 12, color }}>
{isIncome ? "+" : "-"}¥{fmt(tx.amount)}
</div>
<div style={{ fontSize: 9, color: tx.fixed ? C.cyan : C.textDim }}>
{tx.fixed ? "固定費" : "変動費"}
</div>
</div>
</div>
);
};

/** 取引入力モーダル */
const TxModal = ({ type, onClose, onSave }) => {
const [form, setForm] = useState({
date: today(), amount: "", category: type === "income" ? "給与" : "食費",
memo: "", fixed: false, tags: "",
});
const upd = (key, val) => setForm((f) => ({ ...f, [key]: val }));

const cats = type === "income" ? INCOME_CATS : EXPENSE_CATS;
const typeColor = type === "income" ? C.green : type === "expense" ? C.pink : C.yellow;
const typeLabel = type === "income" ? "収入登録" : type === "expense" ? "支出登録" : "振替";
const typeIcon = type === "income" ? "arrowUp" : type === "expense" ? "arrowDn" : "transfer";

const handleSave = () => {
if (!form.amount) return;
onSave({
...form,
type,
amount: Number(form.amount),
tags: form.tags ? form.tags.split(",").map((t) => t.trim()).filter(Boolean) : [],
});
};

return (
<div style={{
position: "fixed", inset: 0,
background: "rgba(0,0,0,0.75)", backdropFilter: "blur(6px)",
zIndex: 300, display: "flex", alignItems: "flex-end",
}}>
<div style={{
background: C.card2,
borderRadius: "20px 20px 0 0",
border: `1px solid ${alpha(typeColor, 0.4)}`,
padding: "20px",
width: "100%", maxWidth: 480, margin: "0 auto",
boxShadow: `0 -8px 40px rgba(0,0,0,0.6), ${glowStrong(typeColor)}`,
}}>
{/* ハンドルバー */}
<div style={{ width: 36, height: 3, background: C.border, borderRadius: 99, margin: "0 auto 16px" }} />

{/* ヘッダー */}
<div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 16 }}>
<div style={{ display: "flex", alignItems: "center", gap: 8 }}>
<IconBox icon={typeIcon} color={typeColor} size={32} />
<span style={{ fontSize: 14, fontWeight: 700, color: C.text }}>{typeLabel}</span>
</div>
<button onClick={onClose} style={{ background: "none", border: "none", cursor: "pointer" }}>
<Icon name="close" size={18} color={C.textSub} />
</button>
</div>

{/* 金額 (大きく表示) */}
<div style={{ marginBottom: 14 }}>
<div style={{ fontSize: 10, color: C.textSub, marginBottom: 4 }}>金額</div>
<input
type="number"
value={form.amount}
placeholder="0"
onChange={(e) => upd("amount", e.target.value)}
style={{
width: "100%", background: C.input,
border: `1px solid ${alpha(typeColor, 0.5)}`,
borderRadius: 12, padding: "14px 16px",
fontSize: 22, fontFamily: "Orbitron, monospace",
color: typeColor, textAlign: "center",
boxShadow: glow(typeColor, 6), outline: "none",
}}
/>
</div>

{/* 日付 + メモ */}
<div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 8, marginBottom: 12 }}>
<Field label="日付" type="date" value={form.date} onChange={(v) => upd("date", v)} color={typeColor} />
<Field label="メモ" value={form.memo} onChange={(v) => upd("memo", v)} color={typeColor} />
</div>

{/* カテゴリー選択 */}
<div style={{ marginBottom: 12 }}>
<div style={{ fontSize: 10, color: C.textSub, marginBottom: 6 }}>カテゴリー</div>
<div style={{ display: "flex", flexWrap: "wrap", gap: 5 }}>
{cats.map(({ name, color }) => {
const active = form.category === name;
return (
<button
key={name}
onClick={() => upd("category", name)}
style={{
background: active ? alpha(color, 0.2) : alpha(color, 0.05),
border: `1px solid ${alpha(color, active ? 0.7 : 0.2)}`,
borderRadius: 99, padding: "5px 10px",
fontSize: 10, color: active ? color : C.textSub,
fontWeight: active ? 700 : 400,
cursor: "pointer", fontFamily: "inherit",
boxShadow: active ? glow(color, 4) : "none",
transition: "all 0.15s",
}}
>
{name}
</button>
);
})}
</div>
</div>

{/* 固定費トグル */}
<Toggle checked={form.fixed} onChange={(v) => upd("fixed", v)} label="固定費として登録" />

{/* 登録ボタン */}
<button
onClick={handleSave}
style={{
width: "100%",
background: `linear-gradient(135deg, ${alpha(typeColor, 0.35)}, ${alpha(typeColor, 0.15)})`,
border: `1px solid ${alpha(typeColor, 0.6)}`,
borderRadius: 14, padding: 14,
fontSize: 14, fontWeight: 700,
color: typeColor, cursor: "pointer", fontFamily: "inherit",
boxShadow: glowStrong(typeColor),
display: "flex", alignItems: "center", justifyContent: "center", gap: 8,
}}
>
<Icon name="check" size={16} color={typeColor} />
登録する
</button>
</div>
</div>
);
};

/** ボトムナビゲーション */
const TABS = [
{ id: "home", icon: "home", label: "ホーム", color: C.cyan },
{ id: "kakeibo", icon: "book", label: "家計簿", color: C.green },
{ id: "assets", icon: "wallet", label: "資産", color: C.purple },
{ id: "subs", icon: "repeat", label: "サブスク", color: C.orange },
{ id: "reports", icon: "chart", label: "レポート", color: C.pink },
{ id: "future", icon: "star", label: "予測", color: C.yellow },
{ id: "tax", icon: "bulb", label: "節税", color: C.cyan },
];

const BottomNav = ({ tab, setTab }) => (
<div style={{
position: "fixed", bottom: 0, left: 0, right: 0, zIndex: 150,
background: "rgba(11,15,26,0.97)", backdropFilter: "blur(16px)",
borderTop: `1px solid ${C.border}`,
display: "flex", justifyContent: "space-around",
padding: "8px 0 14px",
}}>
{TABS.map(({ id, icon, label, color }) => {
const active = tab === id;
return (
<button
key={id}
onClick={() => setTab(id)}
style={{
background: "none", border: "none", cursor: "pointer",
display: "flex", flexDirection: "column", alignItems: "center", gap: 3,
flex: 1, opacity: active ? 1 : 0.4,
transition: "opacity 0.15s",
}}
>
<div style={{
width: 32, height: 32, borderRadius: 10,
background: active ? alpha(color, 0.18) : "transparent",
border: `1px solid ${active ? alpha(color, 0.5) : "transparent"}`,
display: "flex", alignItems: "center", justifyContent: "center",
boxShadow: active ? glow(color) : "none",
transition: "all 0.2s",
}}>
<Icon name={icon} size={15} color={active ? color : C.textSub} />
</div>
<span style={{ fontSize: 8, color: active ? color : C.textSub, fontWeight: active ? 700 : 400 }}>
{label}
</span>
</button>
);
})}
</div>
);

// ============================================================
// 6. TAB SCREENS
// ============================================================

// ─── Home ────────────────────────────────────────────────────
const HomeTab = ({ txs, totalAssets, monthIncome, monthExpense, balance, savingRate, subMonthly, catMap }) => {
const pieData = Object.entries(catMap).map(([name, value]) => ({ name, value }));

return (
<div>
{/* 総資産ヒーロー */}
<div style={{
background: `linear-gradient(135deg, ${C.card2} 0%, #0D1F35 100%)`,
border: `1px solid ${alpha(C.cyan, 0.3)}`,
borderRadius: 20, padding: "20px", marginBottom: 12,
position: "relative", overflow: "hidden",
boxShadow: glowStrong(C.cyan),
}}>
{/* 装飾円 */}
<div style={{ position: "absolute", top: -20, right: -20, width: 120, height: 120, borderRadius: 99, background: alpha(C.cyan, 0.05), border: `1px solid ${alpha(C.cyan, 0.1)}`, pointerEvents: "none" }} />
<div style={{ fontSize: 10, color: C.textSub, letterSpacing: "0.1em", textTransform: "uppercase", marginBottom: 4 }}>総資産</div>
<div style={{ fontFamily: "Orbitron, monospace", fontSize: 28, fontWeight: 900, background: `linear-gradient(90deg,${C.cyan},${C.green})`, WebkitBackgroundClip: "text", WebkitTextFillColor: "transparent", letterSpacing: "-1px" }}>
¥{fmt(totalAssets)}
</div>
<div style={{ display: "flex", gap: 20, marginTop: 12 }}>
{[["前月比", "+¥45,000", C.green, "arrowUp"], ["貯蓄率", `${savingRate}%`, C.yellow, "shield"]].map(([label, val, color, icon]) => (
<div key={label} style={{ display: "flex", alignItems: "center", gap: 6 }}>
<Icon name={icon} size={12} color={color} />
<div>
<div style={{ fontSize: 9, color: C.textSub }}>{label}</div>
<div style={{ fontSize: 13, fontWeight: 700, color, fontFamily: "Orbitron, monospace" }}>{val}</div>
</div>
</div>
))}
</div>
</div>

{/* 4サマリーカード */}
<div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 8, marginBottom: 12 }}>
{[
{ label: "今月収入", value: monthIncome, color: C.green, icon: "arrowUp" },
{ label: "今月支出", value: monthExpense, color: C.pink, icon: "arrowDn" },
{ label: "今月収支", value: balance, color: balance >= 0 ? C.green : C.pink, icon: "trend" },
{ label: "サブスク", value: subMonthly, color: C.orange, icon: "repeat" },
].map(({ label, value, color, icon }) => (
<Card key={label} glowColor={color} style={{ padding: 14 }}>
<div style={{ display: "flex", alignItems: "center", gap: 8, marginBottom: 8 }}>
<IconBox icon={icon} color={color} size={28} />
<span style={{ fontSize: 10, color: C.textSub }}>{label}</span>
</div>
<div style={{ fontFamily: "Orbitron, monospace", fontSize: 14, fontWeight: 900, color }}>
¥{fmt(value)}
</div>
</Card>
))}
</div>

{/* 資産推移グラフ */}
<Card style={{ marginBottom: 12 }}>
<STitle color={C.cyan}>資産推移</STitle>
<ResponsiveContainer width="100%" height={140}>
<AreaChart data={CHART_ASSET_HISTORY}>
<defs>
<linearGradient id="gradAsset" x1="0" y1="0" x2="0" y2="1">
<stop offset="5%" stopColor={C.cyan} stopOpacity={0.25} />
<stop offset="95%" stopColor={C.cyan} stopOpacity={0} />
</linearGradient>
</defs>
<CartesianGrid strokeDasharray="3 3" stroke={C.border} />
<XAxis dataKey="m" tick={{ fontSize: 9, fill: C.textSub }} axisLine={false} tickLine={false} />
<YAxis hide />
<Tooltip content={<NeonTooltip />} />
<Area type="monotone" dataKey="v" name="総資産" stroke={C.cyan} strokeWidth={2} fill="url(#gradAsset)" />
</AreaChart>
</ResponsiveContainer>
</Card>

{/* 予算進捗 */}
<Card style={{ marginBottom: 12 }}>
<STitle color={C.green}>予算進捗</STitle>
{Object.entries(BUDGETS).map(([cat, budget]) => {
const spent = catMap[cat] ?? 0;
const pct = Math.min(100, Math.round((spent / budget) * 100));
const over = pct >= 90;
const bar = over ? C.orange : C.green;
return (
<div key={cat} style={{ marginBottom: 10 }}>
<div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 4 }}>
<span style={{ fontSize: 11, color: C.text }}>{cat}</span>
<span style={{ fontSize: 10, color: over ? C.orange : C.textSub, fontFamily: "Orbitron, monospace" }}>{pct}%</span>
</div>
<div style={{ background: C.input, borderRadius: 99, height: 5 }}>
<div style={{ width: `${pct}%`, height: "100%", borderRadius: 99, background: bar, boxShadow: glow(bar, 4), transition: "width 0.5s" }} />
</div>
<div style={{ fontSize: 9, color: C.textDim, marginTop: 2 }}>¥{fmt(spent)} / ¥{fmt(budget)}</div>
</div>
);
})}
</Card>

{/* 最近の取引 */}
<Card>
<STitle color={C.purple}>最近の取引</STitle>
{[...txs].reverse().slice(0, 5).map((tx) => <TxRow key={tx.id} tx={tx} />)}
</Card>
</div>
);
};

// ─── Kakeibo ─────────────────────────────────────────────────
const KakeiboTab = ({ txs }) => {
const [filter, setFilter] = useState("all");
const shown = [...txs].reverse().filter((t) => filter === "all" || t.type === filter);

return (
<div>
{/* フィルター */}
<div style={{ display: "flex", gap: 8, marginBottom: 12 }}>
{[["all", "すべて", C.cyan], ["income", "収入", C.green], ["expense", "支出", C.pink]].map(([v, l, c]) => (
<NBtn key={v} color={c} onClick={() => setFilter(v)} style={{ flex: 1, textAlign: "center", background: filter === v ? alpha(c, 0.2) : alpha(c, 0.05), borderColor: filter === v ? alpha(c, 0.7) : alpha(c, 0.2) }}>{l}</NBtn>
))}
</div>

{shown.map((tx) => (
<Card key={tx.id} glowColor={tx.type === "income" ? C.green : catColor(tx.category)} style={{ marginBottom: 8, padding: 12 }}>
<TxRow tx={tx} />
</Card>
))}
</div>
);
};

// ─── Assets ──────────────────────────────────────────────────
const AssetsTab = ({ assets }) => {
const total = assets.reduce((s, a) => s + a.balance, 0);
const pieAssets = assets.filter((a) => a.balance > 0);

return (
<div>
{/* 総資産ヒーロー */}
<div style={{
background: `linear-gradient(135deg, ${C.card2}, #0D1F35)`,
border: `1px solid ${alpha(C.purple, 0.4)}`,
borderRadius: 20, padding: "20px", marginBottom: 12,
boxShadow: glowStrong(C.purple),
}}>
<div style={{ fontSize: 10, color: C.textSub, letterSpacing: "0.1em", textTransform: "uppercase" }}>総資産</div>
<div style={{ fontFamily: "Orbitron, monospace", fontSize: 26, fontWeight: 900, color: C.purple }}>¥{fmt(total)}</div>
</div>

{/* 口座一覧 */}
{assets.map((a, i) => {
const ac = ASSET_COLORS[i % ASSET_COLORS.length];
return (
<Card key={a.id} glowColor={ac} style={{ marginBottom: 8, padding: 14 }}>
<div style={{ display: "flex", alignItems: "center", justifyContent: "space-between" }}>
<div style={{ display: "flex", alignItems: "center", gap: 10 }}>
<IconBox icon={ASSET_TYPE[a.type]?.icon ?? "wallet"} color={ac} size={38} />
<div>
<div style={{ fontSize: 12, fontWeight: 600, color: C.text }}>{a.name}</div>
<div style={{ fontSize: 9, color: C.textSub }}>{ASSET_TYPE[a.type]?.label}</div>
</div>
</div>
<div style={{ fontFamily: "Orbitron, monospace", fontWeight: 700, fontSize: 14, color: a.balance < 0 ? C.pink : ac }}>
{a.balance < 0 ? "-" : ""}¥{fmt(a.balance)}
</div>
</div>
</Card>
);
})}

{/* 内訳円グラフ */}
<Card style={{ marginTop: 4 }}>
<STitle color={C.purple}>資産内訳</STitle>
<ResponsiveContainer width="100%" height={200}>
<PieChart>
<Pie data={pieAssets} dataKey="balance" nameKey="name" cx="50%" cy="50%" outerRadius={80} paddingAngle={4}>
{pieAssets.map((_, i) => (
<Cell key={i} fill={ASSET_COLORS[i % ASSET_COLORS.length]} style={{ filter: `drop-shadow(0 0 5px ${ASSET_COLORS[i % ASSET_COLORS.length]}90)` }} />
))}
</Pie>
<Tooltip content={<NeonTooltip />} />
<Legend iconSize={8} wrapperStyle={{ fontSize: 9, color: C.textSub }} />
</PieChart>
</ResponsiveContainer>
</Card>
</div>
);
};

// ─── Subs ─────────────────────────────────────────────────────
const SubsTab = ({ subs }) => {
const subColors = [C.cyan, C.green, C.purple, C.orange];
const monthly = subs.reduce((s, x) => s + x.monthly, 0);

return (
<div>
{/* 合計ヘッダー */}
<div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 8, marginBottom: 12 }}>
{[["月額合計", monthly, C.orange], ["年額換算", monthly * 12, C.yellow]].map(([label, val, color]) => (
<div key={label} style={{
background: `linear-gradient(135deg, ${alpha(color, 0.2)}, ${alpha(color, 0.08)})`,
border: `1px solid ${alpha(color, 0.4)}`,
borderRadius: 16, padding: 14,
boxShadow: glow(color, 10),
}}>
<div style={{ fontSize: 9, color: C.textSub, marginBottom: 4 }}>{label}</div>
<div style={{ fontFamily: "Orbitron, monospace", fontSize: 16, fontWeight: 900, color }}>¥{fmt(val)}</div>
</div>
))}
</div>

{/* サブスク一覧 */}
{subs.map((s, i) => {
const sc = subColors[i % subColors.length];
return (
<Card key={s.id} glowColor={sc} style={{ marginBottom: 8, padding: 14 }}>
<div style={{ display: "flex", alignItems: "center", justifyContent: "space-between" }}>
<div style={{ display: "flex", alignItems: "center", gap: 10 }}>
<IconBox icon="repeat" color={sc} size={36} />
<div>
<div style={{ fontSize: 12, fontWeight: 600, color: C.text }}>{s.name}</div>
<div style={{ fontSize: 9, color: C.textSub }}>次回: {s.next} · {s.method}</div>
</div>
</div>
<div style={{ fontFamily: "Orbitron, monospace", fontWeight: 700, fontSize: 12, color: sc }}>
¥{fmt(s.monthly)}<span style={{ fontSize: 8, color: C.textSub }}>/月</span>
</div>
</div>
</Card>
);
})}
</div>
);
};

// ─── Reports ──────────────────────────────────────────────────
const ReportsTab = ({ txs, catMap }) => {
const pieData = Object.entries(catMap).map(([name, value]) => ({ name, value }));
const fixedTotal = txs.filter((t) => t.type === "expense" && t.fixed).reduce((s, t) => s + t.amount, 0);
const varTotal = txs.filter((t) => t.type === "expense" && !t.fixed).reduce((s, t) => s + t.amount, 0);
const totalExp = fixedTotal + varTotal;

return (
<div>
{/* 月別収支棒グラフ */}
<Card style={{ marginBottom: 12 }}>
<STitle color={C.pink}>月別収支</STitle>
<ResponsiveContainer width="100%" height={170}>
<BarChart data={CHART_MONTHLY} barCategoryGap="30%">
<CartesianGrid strokeDasharray="3 3" stroke={C.border} />
<XAxis dataKey="m" tick={{ fontSize: 9, fill: C.textSub }} axisLine={false} tickLine={false} />
<YAxis hide />
<Tooltip content={<NeonTooltip />} />
<Bar dataKey="inc" name="収入" fill={C.green} radius={[4, 4, 0, 0]} />
<Bar dataKey="exp" name="支出" fill={C.pink} radius={[4, 4, 0, 0]} />
</BarChart>
</ResponsiveContainer>
</Card>

{/* カテゴリー円グラフ */}
<Card style={{ marginBottom: 12 }}>
<STitle color={C.purple}>カテゴリー別支出</STitle>
<ResponsiveContainer width="100%" height={200}>
<PieChart>
<Pie data={pieData} dataKey="value" nameKey="name" cx="50%" cy="50%" outerRadius={78} paddingAngle={3}>
{pieData.map((_, i) => (
<Cell key={i} fill={PIE_COLORS[i % PIE_COLORS.length]} style={{ filter: `drop-shadow(0 0 5px ${PIE_COLORS[i % PIE_COLORS.length]}90)` }} />
))}
</Pie>
<Tooltip content={<NeonTooltip />} />
<Legend iconSize={7} wrapperStyle={{ fontSize: 9, color: C.textSub }} />
</PieChart>
</ResponsiveContainer>
</Card>

{/* 固定費 vs 変動費 */}
<Card>
<STitle color={C.cyan}>固定費 vs 変動費</STitle>
{[["固定費", fixedTotal, C.cyan], ["変動費", varTotal, C.orange]].map(([label, val, color]) => (
<div key={label} style={{ marginBottom: 12 }}>
<div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 5 }}>
<div style={{ display: "flex", alignItems: "center", gap: 6 }}>
<div style={{ width: 3, height: 12, background: color, borderRadius: 99, boxShadow: glow(color) }} />
<span style={{ fontSize: 11, color: C.text }}>{label}</span>
</div>
<span style={{ fontFamily: "Orbitron, monospace", fontSize: 12, fontWeight: 700, color }}>¥{fmt(val)}</span>
</div>
<div style={{ background: C.input, borderRadius: 99, height: 6 }}>
<div style={{ width: `${totalExp > 0 ? Math.round((val / totalExp) * 100) : 0}%`, height: "100%", borderRadius: 99, background: color, boxShadow: glow(color, 4) }} />
</div>
</div>
))}
</Card>
</div>
);
};

// ─── Future ───────────────────────────────────────────────────
const FutureTab = () => {
const [sim, setSim] = useState({ current: 2465000, monthly: 50000, rate: 5, years: 20 });

const calcFuture = (yrs) => {
const r = sim.rate / 100 / 12;
const n = yrs * 12;
if (r === 0) return sim.current + sim.monthly * n;
return Math.round(sim.current * Math.pow(1 + r, n) + sim.monthly * ((Math.pow(1 + r, n) - 1) / r));
};

const chartData = [1, 5, 10, 15, 20, 25, 30].map((y) => ({ year: `${y}年`, total: calcFuture(y) }));
const resultYears = [5, 10, 20, 30];
const resColors = [C.cyan, C.green, C.yellow, C.orange];

return (
<div>
{/* 入力フォーム */}
<Card style={{ marginBottom: 12 }}>
<STitle color={C.yellow}>シミュレーション設定</STitle>
{[
{ label: "現在資産 (円)", key: "current", step: 100000 },
{ label: "毎月積立額 (円)", key: "monthly", step: 1000 },
{ label: "想定利回り (%)", key: "rate", step: 0.1 },
{ label: "運用年数", key: "years", step: 1 },
].map(({ label, key, step }) => (
<Field
key={key}
label={label}
type="number"
step={step}
value={sim[key]}
onChange={(v) => setSim((s) => ({ ...s, [key]: Number(v) }))}
color={C.yellow}
/>
))}
</Card>

{/* 結果カード */}
<div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 8, marginBottom: 12 }}>
{resultYears.map((y, i) => {
const col = resColors[i];
return (
<div key={y} style={{
background: `linear-gradient(135deg, ${alpha(col, 0.2)}, ${alpha(col, 0.08)})`,
border: `1px solid ${alpha(col, 0.4)}`,
borderRadius: 16, padding: 14,
boxShadow: glow(col, 10),
}}>
<div style={{ fontSize: 10, color: C.textSub }}>{y}年後</div>
<div style={{ fontFamily: "Orbitron, monospace", fontSize: 15, fontWeight: 900, color: col, marginTop: 2 }}>¥{fmt(calcFuture(y))}</div>
<div style={{ fontSize: 9, color: C.textDim, marginTop: 2 }}>+¥{fmt(calcFuture(y) - sim.current)}</div>
</div>
);
})}
</div>

{/* 成長曲線 */}
<Card>
<STitle color={C.yellow}>資産成長シミュレーション</STitle>
<ResponsiveContainer width="100%" height={200}>
<AreaChart data={chartData}>
<defs>
<linearGradient id="gradFuture" x1="0" y1="0" x2="0" y2="1">
<stop offset="5%" stopColor={C.yellow} stopOpacity={0.3} />
<stop offset="95%" stopColor={C.yellow} stopOpacity={0} />
</linearGradient>
</defs>
<CartesianGrid strokeDasharray="3 3" stroke={C.border} />
<XAxis dataKey="year" tick={{ fontSize: 9, fill: C.textSub }} axisLine={false} tickLine={false} />
<YAxis hide />
<Tooltip content={<NeonTooltip />} />
<Area type="monotone" dataKey="total" name="予測資産" stroke={C.yellow} strokeWidth={2} fill="url(#gradFuture)" />
</AreaChart>
</ResponsiveContainer>
</Card>
</div>
);
};

// ─── Tax ──────────────────────────────────────────────────────
const TaxTab = () => {
// iDeCo
const [ideco, setIdeco] = useState({ income: 5000000, monthly: 23000 });
const annualIdeco = ideco.monthly * 12;
const incomeTaxRate = ideco.income <= 3300000 ? 0.10 : ideco.income <= 6950000 ? 0.20 : ideco.income <= 9000000 ? 0.23 : 0.33;
const idecoSavings = {
income: Math.round(annualIdeco * incomeTaxRate),
resident: Math.round(annualIdeco * 0.10),
};
idecoSavings.total = idecoSavings.income + idecoSavings.resident;

// ふるさと納税
const [furu, setFuru] = useState({ income: 5000000, family: "single" });
const furuTable = {
single: { 3000000: 28000, 4000000: 42000, 5000000: 61000, 6000000: 77000, 7000000: 109000 },
married: { 3000000: 19000, 4000000: 33000, 5000000: 49000, 6000000: 66000, 7000000: 98000 },
};
const furuBracket = [3000000, 4000000, 5000000, 6000000, 7000000].find((b) => furu.income <= b) ?? 7000000;
const furuLimit = furuTable[furu.family]?.[furuBracket] ?? 100000;

return (
<div>
{/* iDeCo */}
<Card glowColor={C.cyan} style={{ marginBottom: 12 }}>
<STitle color={C.cyan}>iDeCoシミュレーター</STitle>
<Field label="年収 (円)" type="number" step={100000} value={ideco.income} onChange={(v) => setIdeco((s) => ({ ...s, income: Number(v) }))} color={C.cyan} />
<Field label="毎月掛金 (円)" type="number" step={1000} value={ideco.monthly} onChange={(v) => setIdeco((s) => ({ ...s, monthly: Number(v) }))} color={C.cyan} />
<div style={{ background: alpha(C.cyan, 0.08), border: `1px solid ${alpha(C.cyan, 0.3)}`, borderRadius: 12, padding: 12, marginTop: 4 }}>
{[["所得税軽減/年", idecoSavings.income, C.green], ["住民税軽減/年", idecoSavings.resident, C.cyan], ["年間節税合計", idecoSavings.total, C.yellow]].map(([label, val, color], i) => (
<div key={label} style={{ display: "flex", justifyContent: "space-between", padding: "5px 0", borderTop: i > 0 ? `1px solid ${C.border}` : "none" }}>
<span style={{ fontSize: 10, color: C.textSub }}>{label}</span>
<span style={{ fontFamily: "Orbitron, monospace", fontSize: i === 2 ? 13 : 11, fontWeight: 700, color }}>¥{fmt(val)}</span>
</div>
))}
</div>
</Card>

{/* ふるさと納税 */}
<Card glowColor={C.orange}>
<STitle color={C.orange}>ふるさと納税シミュレーター</STitle>
<Field label="年収 (円)" type="number" step={100000} value={furu.income} onChange={(v) => setFuru((s) => ({ ...s, income: Number(v) }))} color={C.orange} />
<div style={{ marginBottom: 12 }}>
<div style={{ fontSize: 10, color: C.textSub, marginBottom: 6 }}>家族構成</div>
<div style={{ display: "flex", gap: 8 }}>
{[["single", "独身"], ["married", "配偶者あり"]].map(([val, label]) => (
<button key={val} onClick={() => setFuru((s) => ({ ...s, family: val }))} style={{
flex: 1, background: furu.family === val ? alpha(C.orange, 0.2) : alpha(C.orange, 0.05),
border: `1px solid ${furu.family === val ? alpha(C.orange, 0.7) : alpha(C.orange, 0.2)}`,
borderRadius: 10, padding: 8, fontSize: 11,
fontWeight: furu.family === val ? 700 : 400,
color: furu.family === val ? C.orange : C.textSub,
cursor: "pointer", fontFamily: "inherit",
}}>{label}</button>
))}
</div>
</div>
<div style={{ background: alpha(C.orange, 0.08), border: `1px solid ${alpha(C.orange, 0.3)}`, borderRadius: 12, padding: 12 }}>
{[["控除上限額の目安", `¥${fmt(furuLimit)}`, C.orange], ["実質自己負担", "¥2,000", C.green]].map(([label, val, color]) => (
<div key={label} style={{ display: "flex", justifyContent: "space-between", padding: "5px 0" }}>
<span style={{ fontSize: 10, color: C.textSub }}>{label}</span>
<span style={{ fontFamily: "Orbitron, monospace", fontSize: 12, fontWeight: 700, color }}>{val}</span>
</div>
))}
<div style={{ fontSize: 9, color: C.textDim, marginTop: 6 }}>※目安です。正確な金額は税務署にご確認ください。</div>
</div>
</Card>
</div>
);
};

// ============================================================
// 7. APP ROOT
// ============================================================

export default function App() {
const [tab, setTab] = useState("home");
const [txs, setTxs] = useState(INIT_TRANSACTIONS);
const [assets] = useState(INIT_ASSETS);
const [subs] = useState(INIT_SUBSCRIPTIONS);
const [alerts, setAlerts] = useState([
{ id: 1, msg: "娯楽費が先月比 +150% です", type: "warn" },
{ id: 2, msg: "6月の月次レポートが届きました", type: "info" },
]);
const [fab, setFab] = useState(false);
const [modal, setModal] = useState(null); // "income" | "expense" | "transfer" | null

// 集計値 (ホーム・レポートで共用)
const totalAssets = assets.reduce((s, a) => s + a.balance, 0);
const monthIncome = txs.filter((t) => t.type === "income" && t.date.startsWith("2025-06")).reduce((s, t) => s + t.amount, 0);
const monthExpense = txs.filter((t) => t.type === "expense" && t.date.startsWith("2025-06")).reduce((s, t) => s + t.amount, 0);
const balance = monthIncome - monthExpense;
const savingRate = monthIncome > 0 ? Math.round((balance / monthIncome) * 100) : 0;
const subMonthly = subs.reduce((s, x) => s + x.monthly, 0);

// カテゴリー別支出マップ (月次)
const catMap = {};
txs.filter((t) => t.type === "expense" && t.date.startsWith("2025-06")).forEach((t) => {
catMap[t.category] = (catMap[t.category] ?? 0) + t.amount;
});

const handleSaveTx = (tx) => {
setTxs((prev) => [...prev, { ...tx, id: Date.now() }]);
setModal(null);
};

// 現在タブのコンテンツを返す
const renderTab = () => {
switch (tab) {
case "home": return <HomeTab txs={txs} totalAssets={totalAssets} monthIncome={monthIncome} monthExpense={monthExpense} balance={balance} savingRate={savingRate} subMonthly={subMonthly} catMap={catMap} />;
case "kakeibo": return <KakeiboTab txs={txs} />;
case "assets": return <AssetsTab assets={assets} />;
case "subs": return <SubsTab subs={subs} />;
case "reports": return <ReportsTab txs={txs} catMap={catMap} />;
case "future": return <FutureTab />;
case "tax": return <TaxTab />;
default: return null;
}
};

return (
<div style={{ fontFamily: "'Noto Sans JP', 'Hiragino Sans', sans-serif", background: C.bg, minHeight: "100vh", color: C.text }}>

{/* Google Fonts + Global Styles */}
<style>{`
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@400;500;700;900&family=Orbitron:wght@700;900&display=swap');
* { box-sizing: border-box; margin: 0; padding: 0; }
::-webkit-scrollbar { width: 3px; }
::-webkit-scrollbar-thumb { background: #1e3a5f; border-radius: 99px; }
input, select, textarea { font-family: inherit; outline: none; }
button:active { transform: scale(0.95); }
@keyframes slideUp { from { opacity: 0; transform: translateY(14px); } to { opacity: 1; transform: translateY(0); } }
.su { animation: slideUp 0.22s ease both; }
`}</style>

{/* ── ヘッダー ── */}
<div style={{
background: "rgba(11,15,26,0.97)", backdropFilter: "blur(12px)",
borderBottom: `1px solid ${C.border}`,
padding: "12px 16px",
display: "flex", alignItems: "center", justifyContent: "space-between",
position: "sticky", top: 0, zIndex: 100,
}}>
<div style={{ display: "flex", alignItems: "center", gap: 10 }}>
<div style={{
width: 34, height: 34, borderRadius: 10,
background: `linear-gradient(135deg, ${alpha(C.cyan, 0.25)}, ${alpha(C.green, 0.15)})`,
border: `1px solid ${alpha(C.cyan, 0.5)}`,
display: "flex", alignItems: "center", justifyContent: "center",
boxShadow: glowStrong(C.cyan),
}}>
<Icon name="trend" size={18} color={C.cyan} />
</div>
<span style={{ fontFamily: "Orbitron, monospace", fontSize: 16, fontWeight: 900, background: `linear-gradient(90deg,${C.cyan},${C.green})`, WebkitBackgroundClip: "text", WebkitTextFillColor: "transparent" }}>
MoneyGrow
</span>
</div>
<div style={{ display: "flex", alignItems: "center", gap: 8 }}>
{alerts.length > 0 && <div style={{ width: 7, height: 7, borderRadius: 99, background: C.orange, boxShadow: glow(C.orange) }} />}
<IconBox icon="bell" color={alerts.length > 0 ? C.orange : C.textDim} size={32} />
</div>
</div>

{/* ── アラートバナー ── */}
{alerts[0] && (
<div style={{
background: `linear-gradient(90deg, ${alpha(alerts[0].type === "warn" ? C.orange : C.cyan, 0.1)}, transparent)`,
borderBottom: `1px solid ${alpha(alerts[0].type === "warn" ? C.orange : C.cyan, 0.4)}`,
padding: "8px 16px",
display: "flex", alignItems: "center", justifyContent: "space-between",
}}>
<div style={{ display: "flex", alignItems: "center", gap: 8 }}>
<Icon name={alerts[0].type === "warn" ? "warning" : "info"} size={13} color={alerts[0].type === "warn" ? C.orange : C.cyan} />
<span style={{ fontSize: 11, color: alerts[0].type === "warn" ? C.orange : C.cyan }}>{alerts[0].msg}</span>
</div>
<button onClick={() => setAlerts((a) => a.slice(1))} style={{ background: "none", border: "none", cursor: "pointer", color: C.textSub, fontSize: 16 }}>×</button>
</div>
)}

{/* ── メインコンテンツ ── */}
<div className="su" style={{ padding: "12px 12px 84px", maxWidth: 480, margin: "0 auto" }}>
{renderTab()}
</div>

{/* ── FABメニュー ── */}
{fab && (
<div className="su" style={{ position: "fixed", bottom: 88, right: 14, zIndex: 200, display: "flex", flexDirection: "column", gap: 8, alignItems: "flex-end" }}>
{[["収入登録", "income", C.green, "arrowUp"], ["支出登録", "expense", C.pink, "arrowDn"], ["振替", "transfer", C.yellow, "transfer"]].map(([label, type, color, icon]) => (
<button key={type} onClick={() => { setModal(type); setFab(false); }} style={{
background: C.card2,
border: `1px solid ${alpha(color, 0.6)}`,
borderRadius: 24, padding: "10px 16px",
color, fontSize: 12, fontWeight: 700,
cursor: "pointer", fontFamily: "inherit",
display: "flex", alignItems: "center", gap: 8,
boxShadow: `0 4px 20px rgba(0,0,0,0.5), ${glow(color, 8)}`,
}}>
<Icon name={icon} size={13} color={color} />{label}
</button>
))}
</div>
)}

{/* ── FABボタン ── */}
<button
onClick={() => setFab((f) => !f)}
style={{
position: "fixed", bottom: 76, right: 14, zIndex: 200,
width: 48, height: 48, borderRadius: 24,
background: fab
? `linear-gradient(135deg, ${alpha(C.pink, 0.8)}, ${alpha(C.purple, 0.8)})`
: `linear-gradient(135deg, ${alpha(C.cyan, 0.9)}, ${alpha(C.green, 0.9)})`,
border: "none", cursor: "pointer",
display: "flex", alignItems: "center", justifyContent: "center",
boxShadow: fab ? glowStrong(C.pink) : glowStrong(C.cyan),
transform: fab ? "rotate(45deg)" : "rotate(0deg)",
transition: "all 0.2s",
}}
>
<Icon name="plus" size={22} color="#fff" />
</button>

{/* ── モーダル ── */}
{modal && <TxModal type={modal} onClose={() => setModal(null)} onSave={handleSaveTx} />}

{/* ── ボトムナビ ── */}
<BottomNav tab={tab} setTab={setTab} />
</div>
);
}
