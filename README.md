
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SkyPulse — glass forecast terminal</title>
<link rel="icon" type="image/png" href="skypulse-icon.png">
<link rel="apple-touch-icon" href="skypulse-logo.png">
<style>
@import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@300;400;600;700;800&display=swap');

*{margin:0;padding:0;box-sizing:border-box}

:root{
  --bg:#010202;
  --acc:#00ff41;
  --acc2:#00e5ff;
  --dim:rgba(180,255,200,.6);
  --text:#d9ffe8;
  --red:#ff3355;
  --yellow:#ffd766;
  --glass:linear-gradient(160deg,rgba(255,255,255,.12),rgba(255,255,255,.03) 42%,rgba(0,0,0,.28));
  --glass-brd:0,255,140;
  --mono:'JetBrains Mono','Fira Code','Cascadia Code',Consolas,'Courier New',monospace;
  --tempc:#00ff90;
}

html,body{background:var(--bg)}
html{scroll-behavior:smooth}
body{font-family:var(--mono);min-height:100vh;color:var(--text);overflow-x:hidden;text-shadow:0 0 10px rgba(0,255,90,.10)}
::selection{background:rgba(0,255,90,.35);color:#000}
::-webkit-scrollbar{width:10px;height:10px}
::-webkit-scrollbar-track{background:#040805}
::-webkit-scrollbar-thumb{background:linear-gradient(180deg,var(--acc),var(--acc2));border-radius:6px;border:2px solid #040805}

/* ---------- terminal frame ---------- */
.term-bar{position:fixed;top:0;left:0;right:0;height:38px;z-index:50;
  background:linear-gradient(180deg,rgba(18,26,20,.85),rgba(6,10,8,.9));
  backdrop-filter:blur(20px) saturate(160%);
  border-bottom:1px solid rgba(var(--glass-brd),.14);
  display:flex;align-items:center;gap:10px;padding:0 14px;font-size:12px;color:var(--dim);letter-spacing:1px;user-select:none}
.splogo{flex-shrink:0;filter:drop-shadow(0 0 8px rgba(0,255,90,.55));animation:logoPulse 3.4s ease-in-out infinite}
@keyframes logoPulse{0%,100%{transform:scale(1);filter:drop-shadow(0 0 6px rgba(0,255,90,.45))}50%{transform:scale(1.1);filter:drop-shadow(0 0 16px rgba(0,229,255,.8))}}
.bar-title{margin-left:14px;overflow:hidden;white-space:nowrap;width:260px}
.bar-track{display:inline-block;animation:marqueeT 30s linear infinite;padding-left:100%}
@keyframes marqueeT{0%{transform:translateX(0)}100%{transform:translateX(-100%)}}
.bar-title b{color:var(--acc)}
.bar-right{margin-left:auto;display:flex;gap:16px;align-items:center;color:var(--acc)}
.bar-right .glyph{animation:blink 2.2s ease infinite}

/* CRT */
.scanlines{position:fixed;inset:0;z-index:55;pointer-events:none;mix-blend-mode:multiply;opacity:.5;
  background:repeating-linear-gradient(0deg,rgba(0,0,0,.2) 0 1px,transparent 1px 3px)}
.crt{position:fixed;inset:0;z-index:56;pointer-events:none;background:radial-gradient(ellipse at center,transparent 60%,rgba(0,0,0,.5) 100%);animation:flicker 9s infinite}
@keyframes flicker{0%,100%{opacity:1}3%{opacity:.96}4%{opacity:1}38%{opacity:1}39.5%{opacity:.94}41%{opacity:1}63%{opacity:1}64%{opacity:.97}65%{opacity:1}81%{opacity:.98}82%{opacity:1}}

/* ---------- bg & fx ---------- */
.bg{position:fixed;inset:0;z-index:-4;transition:opacity 1.4s ease;opacity:0}
.bg.active{opacity:1}
.bg-tint{position:fixed;inset:0;z-index:-3;pointer-events:none;transition:opacity 1.4s ease;opacity:0}
.bg-tint.active{opacity:1}
.orb{position:fixed;border-radius:50%;filter:blur(110px);z-index:-3;opacity:.13;animation:orbFloat 22s ease-in-out infinite;transition:background 1.6s ease}
.orb-1{width:420px;height:420px;top:-120px;left:-120px}
.orb-2{width:320px;height:320px;bottom:-70px;right:-70px;animation-delay:-8s}
.orb-3{width:240px;height:240px;top:45%;left:40%;animation-delay:-15s}
@keyframes orbFloat{0%,100%{transform:translate(0,0) scale(1)}25%{transform:translate(60px,-40px) scale(1.12)}50%{transform:translate(-40px,60px) scale(.88)}75%{transform:translate(40px,40px) scale(1.06)}}

/* ---------- floating glass shards ---------- */
.glass-shards{position:fixed;inset:0;z-index:-1;pointer-events:none;overflow:hidden;will-change:transform}
.gshard{position:absolute;opacity:.9;will-change:transform;
  background:linear-gradient(150deg,rgba(255,255,255,.30),rgba(0,255,120,.14) 35%,rgba(255,255,255,.05) 60%,rgba(0,229,255,.10));
  -webkit-backdrop-filter:blur(20px) saturate(180%);backdrop-filter:blur(20px) saturate(180%);
  border:1px solid rgba(255,255,255,.28);
  border-top-color:rgba(255,255,255,.5);
  box-shadow:inset 0 1px 0 rgba(255,255,255,.35),inset 0 -14px 30px rgba(0,40,20,.18),0 30px 80px rgba(0,0,0,.35),0 0 40px rgba(0,255,120,.10);
  animation:shardFloat linear infinite}
.gshard::after{content:'';position:absolute;inset:-1px;border-radius:inherit;pointer-events:none;
  background:linear-gradient(120deg,rgba(255,255,255,.35),transparent 32%,transparent 62%,rgba(0,229,255,.12))}
.s-blob{border-radius:44% 56% 62% 38%/48% 40% 60% 52%;animation-name:shardBlob}
.s-square{border-radius:26%;animation-name:shardTumble}
.s-ring{border-radius:50%;background:linear-gradient(150deg,rgba(255,255,255,.16),rgba(0,229,255,.05),rgba(255,255,255,.02));
  border:2px double rgba(255,255,255,.35);box-shadow:inset 0 0 36px rgba(255,255,255,.12),0 24px 60px rgba(0,0,0,.25);animation-name:shardSpin}
.s-pill{border-radius:999px;animation-name:shardFloat}
.s-circle{border-radius:50%;animation-name:shardPulse}
.s-diamond{border-radius:16px;transform:rotate(45deg);animation-name:shardSpinRev}
.s-blob.lg{opacity:.7}

@keyframes shardFloat{0%,100%{transform:translateY(0) rotate(0deg)}50%{transform:translateY(-34px) rotate(6deg)}}
@keyframes shardBlob{0%,100%{transform:translateY(0) rotate(0deg) scale(1);border-radius:44% 56% 62% 38%/48% 40% 60% 52%}50%{transform:translateY(-26px) rotate(8deg) scale(1.06);border-radius:58% 42% 38% 62%/56% 60% 40% 44%}}
@keyframes shardTumble{0%,100%{transform:translateY(0) rotate(0deg)}50%{transform:translateY(-20px) rotate(-14deg)}}
@keyframes shardSpin{0%{transform:translateY(0) rotate(0deg)}100%{transform:translateY(-28px) rotate(360deg)}}
@keyframes shardSpinRev{0%,100%{transform:translateY(0) rotate(45deg)}50%{transform:translateY(-24px) rotate(-30deg)}}
@keyframes shardPulse{0%,100%{transform:translateY(0) scale(1);opacity:.8}50%{transform:translateY(-18px) scale(1.25);opacity:1}}

@media (max-width:640px){.gshard.s-ring.sm,.gshard.s-diamond,.gshard.s-blob.lg{display:none}}

/* ---------- scroll transitions ---------- */

/* city-switch wipe */
#wipe{position:fixed;inset:0;z-index:84;pointer-events:none;opacity:0;
  background:radial-gradient(circle at 50% 45%,rgba(0,229,255,.18),rgba(0,255,90,.08) 40%,rgba(0,6,4,.85) 75%)}
#wipe.on{animation:wipeFade .8s cubic-bezier(.22,1,.36,1) both}
@keyframes wipeFade{0%{opacity:0;transform:scale(.5)}35%{opacity:1;transform:scale(1)}100%{opacity:0;transform:scale(1.25)}}

/* globe location pin */
.g-pin{position:absolute;width:16px;height:16px;transform:translate(-50%,-50%);pointer-events:none;z-index:3;display:none}
.g-pin.show{display:block;animation:pinPop .5s cubic-bezier(.34,1.56,.64,1) both}
.g-pin-dot{position:absolute;inset:2px;border-radius:50%;background:var(--tempc,#ff3355);box-shadow:0 0 14px 3px var(--tempc);animation:blink 1.8s ease infinite}
.g-pin-ring{position:absolute;inset:0;border-radius:50%;border:2px solid var(--tempc,#ff3355);animation:pinRing 1.6s ease infinite}
@keyframes pinRing{0%{transform:scale(.6);opacity:.9}100%{transform:scale(2.6);opacity:0}}
@keyframes pinPop{from{opacity:0;transform:translate(-50%,-50%) scale(.2)}to{opacity:1;transform:translate(-50%,-50%) scale(1)}}
.g-pin-lbl{position:absolute;transform:translate(18px,-36px);pointer-events:none;z-index:3;display:none;
  font-size:10px;letter-spacing:1px;color:#fff;background:rgba(0,10,6,.55);border:1px solid rgba(255,255,255,.3);
  padding:3px 10px;border-radius:20px;backdrop-filter:blur(6px);white-space:nowrap}
.g-pin-lbl.show{display:block;animation:dropIn .4s ease .15s both}
.scroll-hint{display:flex;align-items:center;gap:10px;justify-content:center;margin-top:12px;
  color:var(--dim);font-size:11px;letter-spacing:3px;text-transform:uppercase;opacity:1;transition:opacity .5s ease}
.scroll-hint i{display:block;width:12px;height:12px;border-right:2px solid var(--acc);border-bottom:2px solid var(--acc);
  transform:rotate(45deg);animation:chevBounce 1.6s ease infinite}
@keyframes chevBounce{0%,100%{transform:translateY(0) rotate(45deg);opacity:.4}50%{transform:translateY(6px) rotate(45deg);opacity:1}}
#fxCanvas{position:fixed;inset:0;z-index:-2;pointer-events:none}
#matrix{position:fixed;inset:0;z-index:-4;pointer-events:none;opacity:.12}
.lightning{position:fixed;inset:0;z-index:22;pointer-events:none;background:radial-gradient(circle at 30% 20%,rgba(230,255,240,.95),rgba(0,255,90,.15));opacity:0}
.lightning.flash{animation:bolt .18s ease}
@keyframes bolt{0%{opacity:0}8%{opacity:1}18%{opacity:0}26%{opacity:.85}36%{opacity:0}100%{opacity:0}}
.sun-rays{position:fixed;top:-240px;right:-240px;width:600px;height:600px;z-index:-2;pointer-events:none;opacity:0;transition:opacity 1s ease}
.sun-rays.active{opacity:1}
.sun-rays::before{content:'';position:absolute;inset:0;border-radius:50%;background:radial-gradient(circle,rgba(120,255,150,.28),transparent 65%);animation:sunPulse 4s ease-in-out infinite}
@keyframes sunPulse{0%,100%{transform:scale(1);opacity:.4}50%{transform:scale(1.15);opacity:.85}}

/* =============================================== */
/*                APPLE GLASS SYSTEM                 */
/* =============================================== */
.glass{position:relative;border-radius:28px;overflow:hidden;
  background:var(--glass);
  -webkit-backdrop-filter:blur(26px) saturate(180%);backdrop-filter:blur(26px) saturate(180%);
  border:1px solid rgba(var(--glass-brd),.16);
  border-top-color:rgba(255,255,255,.24);
  box-shadow:0 30px 80px rgba(0,0,0,.55),inset 0 1px 0 rgba(255,255,255,.18),inset 0 -1px 0 rgba(255,255,255,.05)}
.glass::after{content:'';position:absolute;inset:0;pointer-events:none;border-radius:inherit;
  background:linear-gradient(115deg,rgba(255,255,255,.16) 0%,rgba(255,255,255,.02) 28%,transparent 45%)}
.glass::before{content:'';position:absolute;inset:0;pointer-events:none;border-radius:inherit;opacity:0;transition:opacity .5s ease;
  background:linear-gradient(115deg,rgba(255,255,255,.22),transparent 40%,transparent 60%,rgba(255,255,255,.06))}
.glass:hover::before{opacity:1}

/* 3D tilt */
.tilt-space{perspective:1600px}
.tilt{transform-style:preserve-3d;transition:transform .35s ease,opacity .5s ease}
.tilt-move{will-change:transform}

/* ---------- floating 3D glass globe ---------- */
.glass-3d{position:relative;width:300px;height:300px;margin:0 auto 14px;filter:drop-shadow(0 24px 60px rgba(0,0,0,.5))}
.stage3d{position:absolute;inset:0;transform-style:preserve-3d;animation:floatBob 7s ease-in-out infinite}
@keyframes floatBob{0%,100%{transform:translateY(0) rotateX(8deg)}50%{transform:translateY(-20px) rotateX(0deg)}}
.globe-wrap{position:absolute;inset:0;transform-style:preserve-3d;
  transform:rotateX(var(--gx,-10deg)) rotateY(var(--gy,18deg))}
.globe{position:absolute;inset:16%;border-radius:50%;overflow:hidden;
  background:
    radial-gradient(circle at 32% 28%,rgba(255,255,255,.55),rgba(255,255,255,.08) 20%,rgba(30,90,70,.3) 55%,rgba(0,15,10,.7) 78%),
    linear-gradient(160deg,#0b2418,#04120b 70%);
  box-shadow:inset -20px -16px 46px rgba(0,0,0,.65),inset 8px 8px 30px rgba(255,255,255,.28),0 0 70px rgba(0,255,90,.22),0 0 150px rgba(0,229,255,.10)}
.lat-wrap{position:absolute;inset:16%;border-radius:50%;overflow:hidden;pointer-events:none;mix-blend-mode:screen;opacity:.85}
.lat{position:absolute;inset:0;
  background:repeating-linear-gradient(90deg,rgba(0,255,90,.5) 0 2px,transparent 2px 20px);
  -webkit-mask:radial-gradient(circle,#000 62%,transparent 68%);mask:radial-gradient(circle,#000 62%,transparent 68%);
  animation:scrollLats 16s linear infinite}
.lon{position:absolute;inset:0;
  background:repeating-linear-gradient(180deg,rgba(0,229,255,.4) 0 2px,transparent 2px 20px);
  -webkit-mask:radial-gradient(circle,#000 62%,transparent 68%);mask:radial-gradient(circle,#000 62%,transparent 68%);
  animation:scrollLons 22s linear infinite reverse}
@keyframes scrollLats{to{background-position:-320px 0}}
@keyframes scrollLons{to{background-position:0 320px}}
.glass-cap{position:absolute;inset:16%;border-radius:50%;pointer-events:none;
  background:radial-gradient(circle at 50% 34%,rgba(255,255,255,.22),transparent 36%),radial-gradient(circle at 50% 88%,rgba(0,0,0,.5),transparent 40%)}
.core-glow{position:absolute;inset:8%;border-radius:50%;background:radial-gradient(circle,var(--tempc,#00ff90),transparent 60%);animation:corePulse 4s ease-in-out infinite}
@keyframes corePulse{0%,100%{opacity:.6;transform:scale(1)}50%{opacity:1;transform:scale(1.06)}}
.gtemp{position:absolute;inset:10%;display:flex;align-items:center;justify-content:center;pointer-events:none;z-index:2}
.gtemp b{font-size:36px;font-weight:800;color:#fff;letter-spacing:-1px;
  text-shadow:0 0 16px var(--tempc),0 0 44px rgba(0,0,0,.4);
  background:rgba(0,10,6,.42);padding:4px 18px;border-radius:40px;
  border:1px solid rgba(255,255,255,.32);border-top-color:rgba(255,255,255,.55);
  backdrop-filter:blur(6px);animation:tempPulse 2.8s ease-in-out infinite}
@keyframes tempPulse{0%,100%{transform:scale(1);box-shadow:0 0 16px var(--tempc)}50%{transform:scale(1.05);box-shadow:0 0 30px var(--tempc)}}

/* mini floating temp-globe */
.mini-globe{position:relative;width:132px;height:132px;border-radius:50%;flex-shrink:0;overflow:hidden;
  background:linear-gradient(160deg,#0b2418,#04120b 70%);
  box-shadow:inset -14px -12px 32px rgba(0,0,0,.65),inset 8px 8px 24px rgba(255,255,255,.28),0 0 54px var(--tempc),0 24px 60px rgba(0,0,0,.5);
  animation:miniBob 5s ease-in-out infinite}
.mini-globe::before{content:'';position:absolute;inset:0;border-radius:50%;pointer-events:none;
  background:radial-gradient(circle at 50% 28%,rgba(255,255,255,.32),transparent 40%)}
.mlat,.mlon{position:absolute;left:0;right:0;top:0;bottom:0;border-radius:50%;pointer-events:none;mix-blend-mode:screen;opacity:.8;
  -webkit-mask:radial-gradient(circle,#000 56%,transparent 74%);mask:radial-gradient(circle,#000 56%,transparent 74%)}
.mlat{background:repeating-linear-gradient(90deg,rgba(0,255,90,.55) 0 2px,transparent 2px 15px);animation:mlat 8s linear infinite}
.mlon{background:repeating-linear-gradient(180deg,rgba(0,229,255,.5) 0 2px,transparent 2px 15px);animation:mlon 11s linear infinite reverse}
@keyframes mlat{to{background-position:-220px 0}}
@keyframes mlon{to{background-position:0 220px}}
.mini-cap{position:absolute;inset:0;border-radius:50%;pointer-events:none;
  background:radial-gradient(circle at 50% 84%,rgba(0,0,0,.55),transparent 44%)}
.mini-read{position:absolute;inset:0;display:flex;align-items:center;justify-content:center;pointer-events:none}
.mini-read b{font-size:30px;font-weight:800;color:#fff;letter-spacing:-1px;
  text-shadow:0 0 16px var(--tempc),0 0 44px rgba(0,0,0,.55);
  background:rgba(0,10,6,.42);padding:3px 15px;border-radius:40px;
  border:1px solid rgba(255,255,255,.32);backdrop-filter:blur(6px)}
@keyframes miniBob{0%,100%{transform:translateY(0) rotate(-2deg)}50%{transform:translateY(-14px) rotate(2deg)}}
.g-ring{position:absolute;inset:-7%;border-radius:50%;border:1px solid rgba(0,255,90,.4);pointer-events:none;animation:ringSpin 18s linear infinite}
.g-ring.r2{inset:-13%;border-color:rgba(0,229,255,.3);animation:ringSpin 28s linear infinite reverse}
@keyframes ringSpin{to{transform:rotate(360deg)}}
.sat{position:absolute;top:-2%;left:50%;width:10px;height:10px;border-radius:50%;background:var(--acc);box-shadow:0 0 18px 4px var(--acc);animation:satOrbit 9s linear infinite}
.sat.s2{top:auto;bottom:-4%;left:20%;width:7px;height:7px;background:var(--acc2);box-shadow:0 0 14px 3px var(--acc2);animation:satOrbit 13s linear infinite reverse}
@keyframes satOrbit{0%{transform:rotate(0) translateX(-150px) rotate(0)}100%{transform:rotate(360deg) translateX(-150px) rotate(-360deg)}}
.ring-tilt{position:absolute;inset:-10%;border-radius:50%;border:1px solid rgba(255,255,255,.22);transform-style:preserve-3d;transform:rotateX(70deg);animation:ringTiltSpin 24s linear infinite;pointer-events:none}
@keyframes ringTiltSpin{to{transform:rotateX(70deg) rotateZ(360deg)}}

/* status chips floating on globe */
.chip3d{position:absolute;font-size:11px;color:var(--acc2);letter-spacing:1px;background:rgba(4,14,8,.5);border:1px solid rgba(var(--glass-brd),.2);padding:4px 10px;border-radius:20px;backdrop-filter:blur(8px);animation:chipFloat 5s ease-in-out infinite;white-space:nowrap}
.chip3d.c1{top:-4%;left:2%}
.chip3d.c2{bottom:-6%;right:-2%;animation-delay:-2.5s}
@keyframes chipFloat{0%,100%{transform:translateY(0) rotate(-2deg)}50%{transform:translateY(-10px) rotate(2deg)}}

/* ---------- banner ---------- */
.wrap{max-width:1160px;margin:0 auto;padding:54px 20px 40px}
.cli-banner{animation:fadeIn .8s ease both;margin-bottom:4px}
@keyframes fadeIn{from{opacity:0}to{opacity:1}}
.ascii-pre{font-size:17px;line-height:1.25;color:var(--acc);text-shadow:0 0 16px rgba(0,255,90,.7);letter-spacing:0;white-space:pre;overflow-x:auto;animation:typeGlow 1.2s ease both}
@keyframes typeGlow{from{opacity:0;filter:blur(4px)}to{opacity:1;filter:blur(0)}}
.cli-line{font-size:14px;color:var(--dim);margin-top:6px;animation:fadeIn 1s ease .25s both}
.cli-line .p{color:var(--acc)}.cli-line .k{color:var(--acc2)}.cli-line .hl{color:var(--yellow)}.cli-line .dt{color:var(--dim)}
.caret{display:inline-block;width:10px;height:16px;background:var(--acc);vertical-align:-3px;margin-left:2px;box-shadow:0 0 12px var(--acc);animation:caretBlink 1s step-end infinite}
@keyframes caretBlink{0%,100%{opacity:1}50%{opacity:0}}
.btns3d{display:flex;gap:10px;justify-content:center;flex-wrap:wrap;margin-top:10px}
.btn-icon{width:52px;height:52px;border-radius:16px;display:flex;align-items:center;justify-content:center;font-size:22px;cursor:pointer;transition:transform .35s var(--cubic,.175,.885,.32,1.275),box-shadow .3s}
.btn-icon:hover{transform:translateY(-6px) scale(1.12);box-shadow:0 18px 40px rgba(0,0,0,.4)}

/* ---------- search ---------- */
.search-wrap{max-width:680px;margin:20px auto 0;padding:0 10px;animation:fadeIn .8s ease .4s both}
.search-box{position:relative;display:flex;align-items:center;gap:6px;
  background:linear-gradient(160deg,rgba(255,255,255,.10),rgba(255,255,255,.03));
  -webkit-backdrop-filter:blur(24px) saturate(180%);backdrop-filter:blur(24px) saturate(180%);
  border:1px solid rgba(var(--glass-brd),.18);border-top-color:rgba(255,255,255,.3);
  border-radius:60px;padding:7px 7px 7px 22px;box-shadow:0 20px 60px rgba(0,0,0,.5),inset 0 1px 0 rgba(255,255,255,.2);
  transition:all .3s ease}
.search-box:focus-within{border-color:rgba(0,255,90,.5);box-shadow:0 20px 70px rgba(0,255,90,.18),inset 0 1px 0 rgba(255,255,255,.2)}
.search-box .prompt{color:var(--acc);font-weight:700;font-size:15px;white-space:nowrap;animation:blink 2.4s ease infinite}
.search-box input{flex:1;border:none;outline:none;background:transparent;color:var(--acc);font-family:var(--mono);font-size:15px;padding:13px 8px;min-width:0;caret-color:var(--acc)}
.search-box input::placeholder{color:rgba(0,210,90,.4)}
.search-btn{border:1px solid rgba(var(--glass-brd),.4);background:linear-gradient(160deg,rgba(0,255,90,.16),rgba(0,255,90,.04));color:var(--acc);font-family:var(--mono);font-weight:700;padding:12px 22px;cursor:pointer;font-size:13px;letter-spacing:1px;border-radius:40px;flex-shrink:0;transition:all .25s ease}
.search-btn:hover{background:var(--acc);color:#000;box-shadow:0 0 28px rgba(0,255,90,.5)}
.search-btn:active{transform:scale(.94)}
.loc-btn{width:46px;height:46px;border-radius:50%;border:1px solid rgba(var(--glass-brd),.4);background:rgba(0,255,90,.08);color:var(--acc);font-size:17px;cursor:pointer;flex-shrink:0;backdrop-filter:blur(10px);transition:all .25s ease}
.loc-btn:hover{background:var(--acc);color:#000;box-shadow:0 0 18px var(--acc);transform:rotate(20deg) scale(1.1)}
.loc-btn.err{border-color:var(--red);animation:shake .5s ease}
@keyframes shake{0%,100%{transform:translateX(0)}15%{transform:translateX(-9px)}30%{transform:translateX(9px)}45%{transform:translateX(-6px)}60%{transform:translateX(6px)}75%{transform:translateX(-3px)}90%{transform:translateX(3px)}}

.autocomplete{position:absolute;top:calc(100% + 8px);left:8px;right:8px;z-index:60;display:none;
  background:rgba(6,16,10,.82);backdrop-filter:blur(30px) saturate(180%);
  border:1px solid rgba(var(--glass-brd),.2);border-radius:22px;box-shadow:0 24px 60px rgba(0,0,0,.7);
  max-height:340px;overflow-y:auto}
.autocomplete.show{display:block;animation:dropIn .25s ease both}
@keyframes dropIn{from{opacity:0;transform:translateY(-14px)}to{opacity:1;transform:translateY(0)}}
.ac-item{display:flex;align-items:center;gap:12px;padding:13px 18px;cursor:pointer;border-bottom:1px solid rgba(255,255,255,.06);transition:all .2s ease}
.ac-item:hover{background:rgba(0,255,90,.08);padding-left:24px}
.ac-item:hover .ac-name{color:var(--acc)}
.ac-flag{font-size:20px;transition:transform .2s ease}.ac-item:hover .ac-flag{transform:scale(1.25)}
.ac-name{font-weight:700;font-size:14px}
.ac-meta{font-size:11px;color:rgba(0,215,95,.65)}
.ac-pop{margin-left:auto;color:var(--acc2);font-size:12px}

/* cmd row */
.cmd-row{max-width:840px;margin:16px auto 0;padding:0 10px;display:flex;flex-wrap:wrap;gap:8px;justify-content:center;align-items:center;animation:fadeIn .9s ease .5s both}
.cmd{display:inline-block;background:linear-gradient(160deg,rgba(255,255,255,.07),rgba(255,255,255,.02));border:1px solid rgba(var(--glass-brd),.18);border-top-color:rgba(255,255,255,.25);color:#89f7a9;font-size:12px;padding:8px 15px;cursor:pointer;border-radius:14px;transition:all .22s ease;letter-spacing:.5px;backdrop-filter:blur(12px)}
.cmd:hover{color:#000;background:var(--acc);border-color:var(--acc);box-shadow:0 8px 26px rgba(0,255,90,.35);transform:translateY(-2px)}
.cmd:active{transform:scale(.93)}
.cmd.flag{color:var(--acc2)}.cmd.flag:hover{background:var(--acc2)}
.cmd.hot{color:#ffd0b8;border-color:rgba(255,130,70,.4)}
.cmd.hot:hover{background:#ff9a5c;border-color:#ff9a5c}
.run-symbol{color:var(--acc);font-weight:800}.run-symbol::before{content:'>';animation:caretBlink 1.2s ease infinite}

.ticker{display:none;margin-top:16px;overflow:hidden;white-space:nowrap;position:relative;-webkit-mask-image:linear-gradient(90deg,transparent,#000 8%,#000 92%,transparent);mask-image:linear-gradient(90deg,transparent,#000 8%,#000 92%,transparent)}
.ticker.show{display:block}
.tracker{display:inline-block;animation:marqueeX 26s linear infinite}
.ticker:hover .tracker{animation-play-state:paused}
@keyframes marqueeX{0%{transform:translateX(0)}100%{transform:translateX(-50%)}}
.tick{display:inline-block;margin:0 6px;padding:7px 16px;border:1px solid rgba(255,255,255,.14);border-radius:30px;color:var(--dim);font-size:12px;cursor:pointer;transition:all .2s ease;backdrop-filter:blur(8px);background:rgba(255,255,255,.03)}
.tick:hover{color:var(--acc);border-color:var(--acc);transform:scale(1.12)}
.tick:hover .tick-x{opacity:1}

/* =============================================
   S E C T I O N   S Y S T E M
============================================= */
.main{max-width:1160px;margin:0 auto;padding:26px 20px 0}
.section{margin-bottom:34px}
.section-head{display:flex;align-items:center;gap:12px;margin-bottom:14px}
.section-head .lab{color:var(--acc);font-weight:700;letter-spacing:2px;font-size:13px;display:flex;align-items:center;gap:8px}
.section-head .lab i{width:8px;height:8px;border-radius:50%;background:var(--acc);box-shadow:0 0 10px var(--acc);animation:blink 1.6s ease infinite}
.section-head .line{flex:1;height:1px;background:repeating-linear-gradient(90deg,rgba(var(--glass-brd),.18) 0 6px,transparent 6px 12px)}
.scroll-fade{opacity:0;transform:translateY(64px) scale(.96);filter:blur(6px);transition:opacity .9s ease,transform 1s cubic-bezier(.22,1,.36,1),filter .9s ease}
.scroll-fade.visible{opacity:1;transform:translateY(0) scale(1);filter:blur(0)}

/* Apple-style scroll build : children cascade in as each section scrolls into view */
.scroll-fade [data-build]{opacity:0;transform:translateY(34px) scale(.97);filter:blur(8px)}
.scroll-fade.visible [data-build]{animation:appleBuild 1s cubic-bezier(.22,1,.36,1) both;animation-delay:var(--d,0s)}
@keyframes appleBuild{from{opacity:0;transform:translateY(46px) scale(.96);filter:blur(10px)}to{opacity:1;transform:translateY(0) scale(1);filter:blur(0)}}

/* ------ hero glass ------ */
.hero-panel{padding:22px 30px 18px;margin-bottom:30px}
.hero-grid{display:grid;grid-template-columns:1fr 1fr;gap:30px;align-items:center}
.hero-left{text-align:center}
.hero-greeting{font-size:13px;color:var(--acc2);letter-spacing:2px;margin-bottom:5px;animation:fadeIn 1s ease both}
.hero-greeting::before{content:'// ';color:var(--acc)}
.hero-title{font-size:44px;font-weight:800;letter-spacing:-.5px;background:linear-gradient(120deg,#fff,var(--acc) 60%,var(--acc2));-webkit-background-clip:text;background-clip:text;-webkit-text-fill-color:transparent;line-height:1.1;animation:typeGlow 1s ease both}
.hero-sub{color:var(--dim);font-size:14px;margin-top:7px;line-height:1.6}
.hero-panel{position:relative;overflow:hidden}

@keyframes popIn{from{opacity:0;transform:scale(.4)}to{opacity:1;transform:scale(1)}}

/* mini pinned-locations globe beside the search bar */
.search-row{display:grid;grid-template-columns:auto 1fr;justify-content:center;align-items:center;gap:24px;margin-top:12px}
.search-row .search-wrap{margin:0;width:100%}
.pin-globe{position:relative;width:min(44vw,250px);height:min(44vw,250px);border-radius:50%;flex-shrink:0;overflow:hidden;z-index:2;
  background:
    radial-gradient(circle at 32% 28%,rgba(255,255,255,.5),rgba(255,255,255,.08) 20%,rgba(30,90,70,.3) 55%,rgba(0,15,10,.7) 78%),
    linear-gradient(160deg,#0b2418,#04120b 70%);
  box-shadow:inset -18px -14px 40px rgba(0,0,0,.65),inset 8px 8px 26px rgba(255,255,255,.25),0 0 52px rgba(0,255,90,.22),0 18px 44px rgba(0,0,0,.45);
  animation:pinBob 6s ease-in-out infinite}
@keyframes pinBob{0%,100%{transform:translateY(0) rotate(-2deg)}50%{transform:translateY(-12px) rotate(2deg)}}
.pg-map{position:absolute;inset:0;opacity:.92;pointer-events:none;
  background-image:url('data:image/png;base64,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');background-size:100% 100%;background-repeat:no-repeat}
.pg-cap{position:absolute;inset:0;border-radius:50%;pointer-events:none;
  background:radial-gradient(circle at 50% 88%,rgba(0,0,0,.55),transparent 44%)}
.pg-pins{position:absolute;inset:0}
.pg-pin{position:absolute;width:14px;height:14px;border-radius:50%;transform:translate(-50%,-50%);cursor:pointer;z-index:3;
  background:radial-gradient(circle at 35% 30%,#fff,#00ff41 55%,#00701d);
  box-shadow:0 0 10px rgba(0,255,90,.9),0 0 22px rgba(0,255,90,.45);border:1px solid rgba(255,255,255,.85);
  transition:transform .2s ease,box-shadow .2s ease}
.pg-pin:hover{transform:translate(-50%,-50%) scale(1.55);box-shadow:0 0 16px var(--acc),0 0 32px rgba(0,229,255,.6)}
.pg-tip{position:absolute;left:50%;top:-32px;transform:translateX(-50%);background:rgba(4,14,8,.94);border:1px solid rgba(0,255,90,.4);color:#b6ffd2;font-size:12px;letter-spacing:.4px;padding:3px 9px;border-radius:8px;white-space:nowrap;pointer-events:none;opacity:0;transition:opacity .2s ease;z-index:6}
.pg-pin:hover .pg-tip{opacity:1}

/* hourly */
.hourly-scroll{display:flex;gap:10px;overflow-x:auto;padding:8px 2px 14px;scrollbar-width:none;position:relative;z-index:2}
.hourly-scroll::-webkit-scrollbar{display:none}
.hour-card{flex:0 0 88px;background:linear-gradient(160deg,rgba(255,255,255,.07),rgba(255,255,255,.02));border:1px solid rgba(255,255,255,.12);border-top-color:rgba(255,255,255,.22);padding:14px 10px;text-align:center;border-radius:18px;cursor:pointer;backdrop-filter:blur(16px);transition:all .3s ease;animation:popIn .5s ease both}
.hour-card:hover{transform:translateY(-8px) scale(1.06);border-color:rgba(0,255,90,.45);box-shadow:0 18px 44px rgba(0,0,0,.45),0 0 22px rgba(0,255,90,.14)}
.hour-card:hover .h-icon{animation:jump .5s ease}
@keyframes jump{0%,100%{transform:translateY(0)}40%{transform:translateY(-12px)}}
.h-icon{font-size:26px;margin:8px 0;display:inline-block}
.h-time{font-size:11px;color:var(--dim)}
.h-temp{font-size:18px;font-weight:700}
.h-now{border-color:rgba(0,255,90,.55);background:linear-gradient(160deg,rgba(0,255,90,.16),rgba(0,255,90,.05));box-shadow:0 0 30px rgba(0,255,90,.22)}.h-now .h-temp{color:var(--acc)}

/* daily */
.daily-list{display:flex;flex-direction:column;gap:8px;position:relative;z-index:2}
.day-row{display:grid;grid-template-columns:84px 40px 1fr 130px;align-items:center;gap:14px;background:linear-gradient(160deg,rgba(255,255,255,.07),rgba(255,255,255,.02));border:1px solid rgba(255,255,255,.11);border-top-color:rgba(255,255,255,.2);padding:13px 18px;border-radius:16px;cursor:pointer;backdrop-filter:blur(14px);transition:all .3s ease;animation:fadeLeft .5s ease both}
@keyframes fadeLeft{from{opacity:0;transform:translateX(-40px)}to{opacity:1;transform:translateX(0)}}
.day-row:hover{transform:translateX(10px);border-color:rgba(0,255,90,.4);background:linear-gradient(160deg,rgba(0,255,90,.1),rgba(0,255,90,.02));box-shadow:0 0 22px rgba(0,255,90,.12)}
.day-row:hover .d-icon{animation:jump .5s ease}
.d-name{font-weight:700;font-size:13px;letter-spacing:1px}
.d-now-tag{display:inline-block;width:8px;height:8px;border-radius:50%;background:var(--acc);box-shadow:0 0 8px var(--acc);margin-left:6px;animation:blink 1.4s ease infinite}
.d-icon{font-size:26px;text-align:center}
.d-range{position:relative;height:7px;background:rgba(0,255,90,.1);border-radius:4px;overflow:hidden}
.d-fill{position:absolute;inset:0;background:linear-gradient(90deg,var(--acc),var(--acc2));border-radius:4px;transform-origin:left;transform:scaleX(0);box-shadow:0 0 12px rgba(0,255,90,.5);transition:transform 1.2s ease}
.day-row.in-view .d-fill{transform:scaleX(var(--grow,1))}
.d-temps{display:flex;justify-content:flex-end;gap:10px;font-size:14px}
.d-high{font-weight:700}.d-low{color:var(--dim)}

/* telemetry */
.telemetry{display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:12px;position:relative;z-index:2}
.tele-item{background:linear-gradient(160deg,rgba(255,255,255,.06),rgba(255,255,255,.01));border:1px solid rgba(255,255,255,.1);border-top-color:rgba(255,255,255,.2);border-radius:18px;padding:16px 18px;backdrop-filter:blur(14px);transition:all .3s ease;animation:slideUp .5s ease both}
.tele-item:hover{transform:translateY(-5px);border-color:rgba(0,229,255,.4);box-shadow:0 16px 40px rgba(0,0,0,.4)}
.tele-ic{font-size:20px;margin-bottom:8px}
.tele-k{font-size:10px;letter-spacing:2px;color:var(--dim);text-transform:uppercase}
.tele-v{font-size:16px;font-weight:700;margin-top:4px;color:var(--acc)}
.tele-v.b{color:var(--acc2)}
.bar-meter{height:5px;background:rgba(255,255,255,.1);border-radius:3px;overflow:hidden;margin-top:8px}
.bar-meter i{display:block;height:100%;width:0%;background:linear-gradient(90deg,var(--acc),var(--acc2));box-shadow:0 0 10px rgba(0,255,90,.5);transition:width 1.4s cubic-bezier(.22,1,.36,1)}

/* strip */
.strip{display:flex;flex-wrap:wrap;gap:10px;margin-top:26px;justify-content:center;position:relative;z-index:2}
.strip-item{font-size:11px;color:var(--dim);border:1px solid rgba(255,255,255,.14);border-top-color:rgba(255,255,255,.25);padding:9px 14px;background:rgba(255,255,255,.03);border-radius:30px;letter-spacing:1px;backdrop-filter:blur(8px);transition:all .25s ease;animation:fadeUp .5s ease both}
@keyframes fadeUp{from{opacity:0;transform:translateY(18px)}to{opacity:1;transform:translateY(0)}}
.strip-item:hover{color:var(--acc);border-color:var(--acc);transform:translateY(-3px);box-shadow:0 8px 22px rgba(0,255,90,.15)}
.strip-item em{font-style:normal;color:var(--acc);font-weight:700}

/* error / loader / footer / fab */
.error-card{display:none;text-align:center;padding:50px 20px;border:1px solid rgba(255,80,120,.4);background:linear-gradient(160deg,rgba(255,60,90,.1),rgba(255,60,90,.02));backdrop-filter:blur(20px);max-width:540px;margin:20px auto;border-radius:28px}
.error-card.show{display:block;animation:shake .55s ease}
.e-emoji{font-size:54px;animation:rollIn .8s ease both}
@keyframes rollIn{from{opacity:0;transform:rotate(-540deg) scale(.5)}to{opacity:1;transform:rotate(0) scale(1)}}
.error-card h3{color:var(--red);margin:12px 0 6px;letter-spacing:2px}
.error-card p{color:var(--dim);font-size:13px}

#loaderOverlay{position:fixed;inset:0;z-index:90;background:rgba(1,3,2,.85);backdrop-filter:blur(20px);display:flex;flex-direction:column;align-items:center;justify-content:center;gap:22px;opacity:0;pointer-events:none;transition:opacity .3s ease}
#loaderOverlay.show{opacity:1;pointer-events:all}
.mini-globe{width:90px;height:90px;border-radius:50%;background:radial-gradient(circle at 32% 30%,rgba(255,255,255,.4),rgba(0,255,90,.15) 30%,#04120b 70%);box-shadow:0 0 40px rgba(0,255,90,.4),inset -10px -8px 24px rgba(0,0,0,.6);animation:loaderBob 1.6s ease-in-out infinite}
@keyframes loaderBob{0%,100%{transform:translateY(0) scale(1)}50%{transform:translateY(-12px) scale(1.05)}}
.spinner-line{width:280px;height:4px;background:rgba(0,255,90,.1);overflow:hidden;position:relative;border-radius:2px}
.spinner-line::after{content:'';position:absolute;top:0;left:-40%;width:40%;height:100%;background:linear-gradient(90deg,transparent,var(--acc),transparent);animation:loadSlide 1.2s linear infinite}
@keyframes loadSlide{0%{left:-40%}100%{left:100%}}
.loader-text{color:var(--acc);font-size:13px;letter-spacing:2px;animation:blink 1.2s ease infinite}
.loader-text::before{content:'$ '}
.loader-percent{color:var(--acc2);font-size:22px;font-weight:800}
@keyframes blink{0%,100%{opacity:.25}50%{opacity:1}}

.footer{text-align:center;padding:36px 20px;color:var(--dim);font-size:12px;letter-spacing:1px;position:relative;z-index:2}
.footer .bq{color:var(--acc)}.footer .bq2{color:var(--acc2)}
.footer a{color:var(--acc2);text-decoration:none}.footer a:hover{color:var(--acc);text-decoration:underline}

.fab{position:fixed;bottom:24px;right:24px;z-index:40;width:54px;height:54px;border-radius:50%;border:1px solid rgba(255,255,255,.2);background:linear-gradient(160deg,rgba(255,255,255,.12),rgba(255,255,255,.03));color:var(--acc);font-size:21px;cursor:pointer;backdrop-filter:blur(16px) saturate(180%);box-shadow:0 18px 50px rgba(0,0,0,.5),inset 0 1px 0 rgba(255,255,255,.2);transition:all .35s ease;animation:fabFloat 3.4s ease-in-out infinite}
.fab:hover{background:var(--acc);color:#000;box-shadow:0 0 34px rgba(0,255,90,.5);transform:rotate(80deg)}
@keyframes fabFloat{0%,100%{transform:translateY(0)}50%{transform:translateY(-9px)}}

@media (max-width:800px){
  .hero-grid{grid-template-columns:1fr;gap:22px}
  .glass-3d{width:min(82vw,300px);height:min(82vw,300px)}
  .term-bar{padding:0 10px}
  .bar-title{width:200px}
}

@media (max-width:640px){
  .wrap{padding:58px 12px 30px}
  .main{padding:18px 12px 0}
  .section{margin-bottom:22px}
  .hero-panel{padding:14px 12px 12px;margin-bottom:20px}
  .hero-title{font-size:clamp(26px,8vw,34px)}
  .hero-greeting{font-size:12px}
  .hero-sub{font-size:13px;padding:0 4px}
  .ascii-pre{font-size:8px;line-height:1.2}
  .cli-line{font-size:12px}
  .search-box input{font-size:16px}
  .search-btn{padding:11px 16px;font-size:12px}
  .loc-btn{width:42px;height:42px}
  .panel{padding:18px 12px!important}
  .day-row{grid-template-columns:66px 30px 1fr;gap:8px;padding:11px 12px}.d-range{display:none}
  .hour-card{flex:0 0 74px;padding:12px 8px}
  .cmd-row{gap:6px}
  .cmd{font-size:11px;padding:7px 11px}
  .tick{font-size:11px;padding:6px 12px}
  .pin-globe{width:min(60vw,170px);height:min(60vw,170px)}
  .search-row{grid-template-columns:1fr;justify-items:center;gap:16px}
  .footer{font-size:11px;padding:26px 12px}
  .fab{width:48px;height:48px;right:14px;bottom:14px;font-size:18px}
  .bar-right .glyph.sm{display:none}
}

@media (max-width:480px){
  .term-bar{font-size:10px;gap:6px}
  .bar-title{width:150px}
  .gtemp b{font-size:26px;padding:3px 14px}
  .chip3d{font-size:10px;padding:3px 8px}
  .autocomplete{border-radius:16px}
  .ac-item{padding:11px 14px}
  .hourly-scroll{gap:8px}
  .section-head .lab{font-size:12px}
  .day-row{grid-template-columns:58px 26px 1fr;gap:6px}
  .hero-title{font-size:clamp(24px,11vw,30px)}
}
@media (prefers-reduced-motion:reduce){*,*::before,*::after{animation-duration:.01ms!important;transition-duration:.01ms!important}}
</style>
</head>
<body>

<!-- terminal frame -->
<div class="term-bar">
  <div class="bar-left" style="display:flex;align-items:center;gap:8px">
    <svg class="splogo" viewBox="0 0 32 32" width="25" height="25" aria-label="SkyPulse" role="img">
      <defs>
        <linearGradient id="splg" x1="0" y1="0" x2="1" y2="1">
          <stop offset="0" stop-color="#00ff41"/><stop offset="1" stop-color="#00e5ff"/>
        </linearGradient>
      </defs>
      <circle cx="16" cy="16" r="13" fill="none" stroke="url(#splg)" stroke-width="2"/>
      <ellipse cx="16" cy="16" rx="13" ry="5" fill="none" stroke="url(#splg)" stroke-width="1.1" opacity=".55"/>
      <path d="M5 19 C9 19 9 11 14 11 C19 14 19 20 24 20 C27 20 28.5 17.5 29 16" fill="none" stroke="#fff" stroke-width="2" stroke-linecap="round"/>
      <circle cx="16" cy="16" r="2.6" fill="url(#splg)"/>
    </svg>
    <span class="bar-title"><b>skypulse://</b>forecast</span>
  </div>
  <div class="bar-right"><span class="glyph">◆</span><span>SHARD 001</span><span>UPTIME 00:12:44</span><span id="barTemp">--°C</span></div>
</div>

<div class="scanlines"></div>
<div class="crt"></div>

<div class="bg active" id="bgTarget"></div>
<div class="bg" id="bgOld"></div>
<div class="bg-tint" id="bgTint"></div>
<div class="orb orb-1"></div>
<div class="orb orb-2"></div>
<div class="orb orb-3"></div>

<!-- floating glass shards -->
<div class="glass-shards" aria-hidden="true">
  <div class="gshard s-blob" style="top:6%;left:-3%;width:260px;height:260px;animation-duration:12s;animation-delay:-1s"></div>
  <div class="gshard s-pill" style="top:20%;right:-1%;width:90px;height:190px;animation-duration:14s;animation-delay:-8s"></div>
  <div class="gshard s-ring" style="right:2%;bottom:14%;width:210px;height:210px;animation-duration:11s;animation-delay:-3s"></div>
  <div class="gshard s-blob lg" style="bottom:-6%;left:8%;width:310px;height:310px;animation-duration:17s;animation-delay:-11s"></div>
  <div class="gshard s-square" style="top:42%;left:-2%;width:140px;height:140px;animation-duration:15s;animation-delay:-6s"></div>
  <div class="gshard s-circle" style="top:3%;right:12%;width:86px;height:86px;animation-duration:9s;animation-delay:-2s"></div>
  <div class="gshard s-diamond" style="top:66%;right:7%;width:150px;height:150px;animation-duration:13s;animation-delay:-5s"></div>
  <div class="gshard s-ring sm" style="top:12%;left:36%;width:120px;height:120px;animation-duration:10s;animation-delay:-7s"></div>
  <div class="gshard s-blob" style="left:55%;bottom:40%;width:180px;height:180px;animation-duration:16s;animation-delay:-4s"></div>
  <div class="gshard s-circle sm" style="bottom:12%;right:26%;width:60px;height:60px;animation-duration:8s;animation-delay:-9s"></div>
</div>

<canvas id="matrix"></canvas>
<canvas id="fxCanvas"></canvas>
<div class="sun-rays" id="sunRays"></div>
<div class="lightning" id="lightning"></div>
<div id="wipe"></div>

<div class="wrap">

  <!-- banner -->
  <div class="cli-banner">
    <pre class="ascii-pre" id="asciiBanner"></pre>
    <div class="cli-line">
      <span class="p">┌──</span><span class="k">(</span><span class="hl">sky@skypulse</span><span class="k">)-</span><span class="p">[</span><span class="k">~</span><span class="p">]</span><span class="dt">─[</span><span class="k">weather</span><span class="dt">]</span>
    </div>
    <div class="cli-line" id="cmdLine"><span class="p">└─$</span> <span id="typedCmd" class="dt"></span><span class="caret"></span></div>
  </div>

  <!-- HERO : 3D globe + search (section 1) -->
  <section class="section scroll-fade hero-panel glass tilt" id="heroPanel">
    <div class="hero-grid">
      <div class="hero-left">
        <div class="hero-greeting">welcome back, explorer</div>
        <div class="hero-title">Sky<span>Pulse</span></div>
        <div class="hero-sub">A weather terminal for every place on Earth.<br>Search, orbit, and feel the sky — live.</div>

        <div class="search-row">
          <div class="pin-globe" id="pinGlobe" title="pinned weather globes">
            <div class="pg-map"></div>
            <div class="pg-cap"></div>
            <div class="pg-pins" id="pgPins"></div>
          </div>

          <div class="search-wrap">
          <div class="search-box">
            <span class="prompt">└─$</span>
            <input type="search" id="searchInput" placeholder="search any city on earth…" autocomplete="off" spellcheck="false">
            <button class="loc-btn" id="locBtn" title="use my location">⌖</button>
            <button class="search-btn" id="searchBtn">run</button>
            <div class="autocomplete" id="autocomplete"></div>
          </div>
        </div>
        </div>

        <div class="btns3d">
          <button class="btn-icon" title="surprise city" onclick="surpriseMe()" style="background:linear-gradient(160deg,rgba(255,255,255,.1),rgba(255,255,255,.02));border:1px solid rgba(255,255,255,.16);backdrop-filter:blur(14px)">🎲</button>
          <button class="btn-icon" title="random coordinates" onclick="fullRandom()" style="background:linear-gradient(160deg,rgba(255,255,255,.1),rgba(255,255,255,.02));border:1px solid rgba(255,255,255,.16);backdrop-filter:blur(14px)">🎯</button>
          <button class="btn-icon" title="unit °C" onclick="setUnit('C')" style="font-family:var(--mono);font-size:15px;color:var(--acc);background:linear-gradient(160deg,rgba(255,255,255,.1),rgba(255,255,255,.02));border:1px solid rgba(255,255,255,.16)" id="btnC">°C</button>
          <button class="btn-icon" title="unit °F" onclick="setUnit('F')" style="font-family:var(--mono);font-size:15px;color:var(--acc2);background:linear-gradient(160deg,rgba(255,255,255,.1),rgba(255,255,255,.02));border:1px solid rgba(255,255,255,.16)" id="btnF">°F</button>
        </div>

        <div class="ticker" id="ticker"><div class="tracker" id="tracker"></div></div>

        <div class="scroll-hint"><i></i>scroll to explore</div>
      </div>

      <div class="hero-right tilt-space">
        <div class="glass-3d">
          <div class="stage3d">
            <div class="globe-wrap">
              <div class="core-glow"></div>
              <div class="globe"></div>
              <div class="lat-wrap"><div class="lat"></div><div class="lon"></div></div>
              <div class="glass-cap"></div>
              <div class="gtemp"><b id="globeTemp">--°</b></div>
              <div class="g-pin" id="gPin"><span class="g-pin-ring"></span><span class="g-pin-dot"></span></div>
              <div class="g-pin-lbl" id="gPinLbl">📍 --</div>
            </div>
            <div class="g-ring"></div>
            <div class="g-ring r2"></div>
            <div class="ring-tilt"></div>
            <div class="sat"></div>
            <div class="sat s2"></div>
            <div class="chip3d c1" id="chipZone">ZONE --</div>
            <div class="chip3d c2" id="chipMode">NIGHT · IDLE</div>
          </div>
        </div>
      </div>
    </div>
  </section>

  <div class="main">
    <div class="error-card" id="errorCard"><div class="e-emoji">💀</div><h3>ERROR :: LOCATION_NOT_FOUND</h3><p id="errorText">city lookup returned nothing. try again.</p></div>

    <!-- SECTION 3 : hourly -->
    <section class="section scroll-fade tilt-space">
      <div class="section-head"><span class="lab"><i></i>hourly_forecast</span><div class="line"></div></div>
      <div class="panel glass tilt" id="hourlySection" style="display:none;padding:28px 22px;">
        <div class="hourly-scroll" id="hourlyScroll"></div>
      </div>
    </section>

    <!-- SECTION 4 : 7-day -->
    <section class="section scroll-fade tilt-space">
      <div class="section-head"><span class="lab"><i></i>7_day_forecast</span><div class="line"></div></div>
      <div class="panel glass tilt" id="dailySection" style="display:none;padding:28px 22px;">
        <div class="daily-list" id="dailyList"></div>
        <div class="strip" id="strip"></div>
      </div>
    </section>

    <!-- SECTION 5 : telemetry -->
    <section class="section scroll-fade tilt-space">
      <div class="section-head"><span class="lab"><i></i>system_telemetry</span><div class="line"></div></div>
      <div class="panel glass tilt" style="padding:28px 22px;">
        <div class="telemetry" id="telemetry"></div>
      </div>
    </section>

    <!-- SECTION 6 : protocol -->
    <section class="section scroll-fade tilt-space">
      <div class="section-head"><span class="lab"><i></i>protocol_notes</span><div class="line"></div></div>
      <div class="glass tilt" style="padding:28px;">
        <div class="strip" style="margin-top:0">
          <div class="strip-item">data source <em>open-meteo.com</em></div>
          <div class="strip-item">latency <em id="latV">--</em></div>
          <div class="strip-item">user-agent <em>skypulse/2.0</em></div>
          <div class="strip-item">hint <em>press "/" to type</em></div>
        </div>
      </div>
    </section>
  </div>
</div>

<div class="footer">
  <span class="bq">┌─[</span>skypulse v2.0<span class="bq">]─[</span>forecast<span class="bq">:~/.weather]</span><br>
  api <span class="bq2">open-meteo.com</span> · made for curious minds · © 2026 <span class="bq">└─$</span><span class="caret"></span>
</div>

<button class="fab" onclick="surpriseMe()" title="surprise me">🎲</button>

<div id="loaderOverlay">
  <div class="mini-globe"></div>
  <div class="loader-percent" id="loadPct">00%</div>
  <div class="spinner-line"></div>
  <div class="loader-text">synchronising with low-earth orbit…</div>
</div>

<script>
/* ================= utils ================= */
const $=id=>document.getElementById(id);
const rand=(a,b)=>a+Math.random()*(b-a);
const lerp=(a,b,t)=>a+(b-a)*t;

/* ================= ASCII banner ================= */
const FONT={
S:['██████','█     ','██████','     █','     █','██████'],
Y:['█   █ ','█   █ ','██████','  ██  ','  ██  ','  ██  '],
K:['█   █ ','█  █  ','█ █   ','██    ','█ █   ','█   █ '],
P:['█████ ','█   █ ','█████ ','█     ','█     ','█     '],
U:['█   █ ','█   █ ','█   █ ','█   █ ','█   █ ','██████'],
L:['█     ','█     ','█     ','█     ','█     ','██████'],
E:['██████','█     ','██████','█     ','█     ','██████']
};
function asciiWord(word){
  const lines=['','','','','',''];
  for(const ch of word.toUpperCase()){
    const f=FONT[ch];if(!f)continue;
    for(let i=0;i<6;i++)lines[i]+=(lines[i]?'   ':'')+f[i];
  }
  return lines.join('\n');
}
$('asciiBanner').textContent=asciiWord('SKYPULSE');

/* ================= Matrix rain ================= */
const mcs=$('matrix'),mcx=mcs.getContext('2d');
const glyphs='アイウエオカキクケコ0123456789ABCDEF#$@'.split('');
let mCols=0,mdrops=[];
function msize(){mcs.width=innerWidth;mcs.height=innerHeight;mCols=Math.floor((mcs.width-6)/16);mdrops=Array(mCols).fill(0).map(()=>Math.random()*-30)}
msize();addEventListener('resize',msize);
function mloop(){
  mcx.fillStyle='rgba(2,6,4,.13)';mcx.fillRect(0,0,mcs.width,mcs.height);
  mcx.font='15px monospace';
  for(let i=0;i<mCols;i++){
    const y=mdrops[i]*16;
    if(y>mcs.height){if(Math.random()>.975)mdrops[i]=0;else mdrops[i]++;}
    else{
      mcx.fillStyle='rgba(0,255,90,.6)';
      mcx.fillText(glyphs[(Math.random()*glyphs.length)|0],i*16,y);
      mdrops[i]++;
    }
  }
  requestAnimationFrame(mloop);
}
mloop();

/* ================= weather FX canvas ================= */
const cv=$('fxCanvas'),cx=cv.getContext('2d');
let fxKind='none',fx=[];
function sz(){cv.width=innerWidth;cv.height=innerHeight}
addEventListener('resize',sz);sz();
function spawnFX(kind){
  fxKind=kind;fx=[];
  if(kind==='none'||kind==='star')return;
  const n=kind==='snow'?90:kind==='rain'?150:kind==='leaf'?40:100;
  for(let i=0;i<n;i++){
    if(kind==='rain')fx.push({y:rand(-500,0),x:rand(0,cv.width),l:rand(14,30),s:rand(10,18)});
    else if(kind==='snow')fx.push({y:rand(-300,0),x:rand(0,cv.width),r:rand(2,5),s:rand(.5,1.8),a:rand(0,.85),wy:rand(-40,40)});
    else if(kind==='leaf')fx.push({y:rand(-400,0),x:rand(0,cv.width),r:rand(3,6),s:rand(.6,1.6),sway:rand(-70,70),col:rand(0,6.28)});
    else fx.push({x:rand(0,cv.width),y:rand(0,cv.height),r:rand(1,3),sx:rand(-.5,.5),sy:rand(-.5,.5),o:rand(.05,.4)});
  }
}
function floop(){
  cx.clearRect(0,0,cv.width,cv.height);
  if(fxKind!=='none')for(const p of fx){
    if(fxKind==='rain'){
      cx.beginPath();cx.moveTo(p.x,p.y);cx.lineTo(p.x-2,p.y+p.l);
      cx.strokeStyle='rgba(120,220,255,.45)';cx.lineWidth=1.4;cx.stroke();
      p.y+=p.s;p.x-=p.s*.25;if(p.y>cv.height)p.y=-p.l;
    }else if(fxKind==='snow'){
      cx.beginPath();cx.arc(p.x+Math.sin(p.y*.01+p.wy)*6,p.y,p.r,0,7);
      cx.fillStyle=`rgba(220,250,255,${p.a})`;cx.fill();
      p.y+=p.s;if(p.y>cv.height){p.y=-10;p.x=rand(0,cv.width)}
    }else if(fxKind==='leaf'){
      p.x+=Math.sin(p.y*.02+p.sway)*.5;p.y+=p.s;
      cx.save();cx.translate(p.x,p.y);cx.rotate(p.col+performance.now()*.002);
      cx.beginPath();cx.ellipse(0,0,p.r,p.r*.5,0,0,7);
      cx.fillStyle='rgba(120,255,140,.7)';cx.fill();cx.restore();
      if(p.y>cv.height)p.y=-20;
    }else{
      cx.beginPath();cx.arc(p.x,p.y,p.r,0,7);
      cx.fillStyle=`rgba(0,255,90,${p.o})`;cx.fill();
      p.x+=p.sx;p.y+=p.sy;if(p.x<0)p.x=cv.width;if(p.x>cv.width)p.x=0;if(p.y<0)p.y=cv.height;if(p.y>cv.height)p.y=0;
    }
  }
  requestAnimationFrame(floop);
}
floop();

/* ================= theme (dark terminal accents) ================= */
const ZONES={
  tropical:{label:'[TROPICAL]',c:'#00ff41'},
  subtropical:{label:'[SUB-TROP]',c:'#7dff8a'},
  warm:{label:'[WARM-TEMP]',c:'#00ff9d'},
  cold:{label:'[COOL-TEMP]',c:'#38bdf8'},
  subpolar:{label:'[SUBPOLAR]',c:'#7dd3fc'},
  polar:{label:'[POLAR]',c:'#c8efff'},
  desert:{label:'[DESERT]',c:'#ffcc55'}
};
const CM={
  clear:{icon:['☀️','🌙'],an:'sun',fx:'star',tiz:'radial-gradient(circle at 40% 15%,rgba(0,255,90,.18),transparent 60%)'},
  cloudy:{icon:['⛅','☁️'],an:'cloud',fx:'dust',tiz:'radial-gradient(circle at 50% 20%,rgba(120,180,160,.14),transparent 60%)'},
  overcast:{icon:['☁️','☁️'],an:'cloud',fx:'dust',tiz:'radial-gradient(circle at 50% 0%,rgba(150,170,180,.14),transparent 65%)'},
  fog:{icon:['🌫️','🌫️'],an:'fog',fx:'dust',tiz:'radial-gradient(circle at 50% 30%,rgba(190,200,205,.1),transparent 65%)'},
  rain:{icon:['🌦️','🌧️'],an:'rain',fx:'rain',tiz:'radial-gradient(circle at 40% 10%,rgba(50,140,220,.22),transparent 60%)'},
  snow:{icon:['🌨️','❄️'],an:'snow',fx:'snow',tiz:'radial-gradient(circle at 40% 10%,rgba(220,245,255,.2),transparent 60%)'},
  storm:{icon:['⛈️','⛈️'],an:'storm',fx:'rain',tiz:'radial-gradient(circle at 30% 10%,rgba(170,70,240,.28),transparent 60%)'}
};
const NAMES={0:'clear sky',1:'mostly clear',2:'partly cloudy',3:'overcast',45:'foggy',48:'rime fog',51:'light drizzle',53:'drizzle',55:'dense drizzle',56:'freezing drizzle',57:'freezing drizzle',61:'light rain',63:'rain',65:'heavy rain',66:'freezing rain',67:'freezing rain',71:'light snow',73:'snow',75:'heavy snow',77:'snow grains',80:'light showers',81:'showers',82:'violent showers',85:'snow showers',86:'heavy snow showers',95:'thunderstorm',96:'thunderstorm + hail',99:'severe storm'};
function condOf(code){if(code>=95)return'storm';if(code>=71&&code<=86)return'snow';if((code>=51&&code<=67)||(code>=80&&code<=82))return'rain';if(code>=45&&code<=48)return'fog';if(code===3)return'overcast';if(code>=0&&code<=2)return'clear';return'cloudy'}
function wName(code){return NAMES[code]||'---'}
function flagOf(cc){return cc?cc.toUpperCase().replace(/./g,c=>String.fromCodePoint(127397+c.charCodeAt(0))):'🌍'}

const bgT=$('bgTarget'),bgO=$('bgOld');
let activeTheme=null;
function applyTheme(zoneKey,cond,isNight){
  const mm=CM[cond];
  const acc=ZONES[zoneKey].c;
  const grad=`linear-gradient(160deg,#000 0%,#020604 35%,#04100a 100%)`;
  const tint=mm.tiz;
  if(!activeTheme){bgT.style.background=grad;bgT.classList.add('active');$('bgTint').style.background=tint;$('bgTint').classList.add('active');}
  else{
    bgT.style.background=grad;bgT.classList.add('active');bgO.classList.remove('active');
    $('bgTint').style.background=tint;$('bgTint').classList.add('active');
  }
  activeTheme={grad,tint};
  const orbs=document.querySelectorAll('.orb');
  const gl=Math.random()<.5?acc:'#00ff41';
  orbs.forEach(o=>o.style.background=gl);
  document.documentElement.style.setProperty('--acc',acc);
  const sr=$('sunRays');
  if(cond==='clear'&&!isNight)sr.classList.add('active');else sr.classList.remove('active');
  const bi=$('bigIcon');
  if(bi){bi.className='big-icon '+mm.an;bi.textContent=mm.icon[isNight?1:0];}
  $('chipZone').textContent='ZONE '+ZONES[zoneKey].label;
  $('chipMode').textContent=(isNight?'NIGHT':'DAY')+' · '+cond.toUpperCase();
  return mm;
}
function pickZone(lat,temp,hum,cond){
  const a=Math.abs(lat);
  if(temp>36&&hum<25&&cond!=='snow')return'desert';
  if(a<16)return'tropical';
  if(a<24)return'subtropical';
  if(a<42)return'warm';
  if(a<57)return'cold';
  if(a<66)return'subpolar';
  return'polar';
}
function tempColor(t){
  if(t>=36)return'#ff5c33';
  if(t>=28)return'#ff8a4d';
  if(t>=20)return'#ffd766';
  if(t>=12)return'#7dff8a';
  if(t>=4)return'#4de0ff';
  return'#9be8ff';
}
const clamp=(v,a,b)=>Math.min(b,Math.max(a,v));

/* ================= state ================= */
let UNIT='C',weather=null,geo=null;
function fmtT(c){return UNIT==='F'?Math.round(c*9/5+32):Math.round(c)}
function uSym(){return UNIT==='F'?'°F':'°C'}
const popFmt=n=>n>=1e6?(n/1e6).toFixed(1)+'M':n>=1e3?(n/1e3).toFixed(0)+'k':n;

function typeLine(el,txt,speed){
  let i=0;el.textContent='';
  (function ty(){if(i<=txt.length){el.textContent=txt.slice(0,i);i++;setTimeout(ty,speed)}})();
}

/* ================= autocomplete ================= */
const input=$('searchInput'),ac=$('autocomplete');
let acTimer=null;
input.addEventListener('input',()=>{
  clearTimeout(acTimer);
  const q=input.value.trim();
  if(q.length<2){ac.classList.remove('show');return}
  acTimer=setTimeout(async()=>{
    try{
      const d=await (await fetch(`https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(q)}&count=10`)).json();
      if(!d.results){ac.classList.remove('show');return}
      const res=d.results.sort((a,b)=>(b.population||0)-(a.population||0)).slice(0,8);
      ac.innerHTML='';
      res.forEach(c=>{
        const el=document.createElement('div');el.className='ac-item';
        el.innerHTML=`<span class="ac-flag">${flagOf(c.country_code)}</span><div><div class="ac-name">${c.name}${c.admin1?' · '+c.admin1:''}</div><div class="ac-meta">${c.country||''} · ${Math.abs(c.latitude).toFixed(1)}°${c.latitude>=0?'N':'S'} · ${c.population?popFmt(c.population)+' pop':'?'}</div></div><span class="ac-pop">▸</span>`;
        el.onclick=()=>{input.value=c.name;ac.classList.remove('show');go(c)};
        ac.appendChild(el);
      });
      ac.classList.add('show');
    }catch(e){ac.classList.remove('show')}
  },300);
});
input.addEventListener('keydown',e=>{if(e.key==='Enter'){ac.classList.remove('show');search()}});
document.addEventListener('click',e=>{if(!e.target.closest('.search-box'))ac.classList.remove('show')});

async function search(){
  const q=input.value.trim();
  if(!q)return;
  showLoad();
  try{
    const d=await (await fetch(`https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(q)}&count=1`)).json();
    if(!d.results||!d.results.length)throw new Error('nf');
    go(d.results.sort((a,b)=>(b.population||0)-(a.population||0))[0]);
  }catch(e){hideLoad();showError('$ city_scan: no match found in 240,000+ cities')}
}
$('locBtn').addEventListener('click',()=>{
  if(!navigator.geolocation){showError('no gps module on this box');return}
  const btn=$('locBtn');btn.textContent='…';
  navigator.geolocation.getCurrentPosition(
    p=>{btn.textContent='⌖';go({latitude:p.coords.latitude,longitude:p.coords.longitude,name:'this location',country:'',country_code:null,timezone:null})},
    ()=>{btn.textContent='⌖';btn.classList.add('err');setTimeout(()=>btn.classList.remove('err'),600);showError('$ gps: permission denied — set manually')},
    {timeout:8000}
  );
});
function surpriseMe(){
  const list=['Tokyo','Reykjavik','Marrakesh','Singapore','Oslo','Quito','Nairobi','Sydney','Cairo','Mumbai','Honolulu','Buenos Aires','Athens','Ulan Bator','Lima','Copenhagen','Kuala Lumpur','Bogota','Auckland','Istanbul','Tehran','Stockholm','Anchorage','Hanoi','Rabat','Tashkent','La Paz','Addis Ababa','Dublin','Bangkok'];
  input.value=list[(Math.random()*list.length)|0];search();
}
function fullRandom(){
  input.value=`random@${rand(-85,85).toFixed(1)},${rand(-180,180).toFixed(1)}`;
  go({latitude:rand(-85,85),longitude:rand(-180,180),name:'random point',country:'',country_code:null,timezone:null});
}

/* ================= mini pinned-locations globe ================= */
const PINS=[['Tokyo',35.68,139.69],['New York',40.71,-74.01],['Sydney',-33.87,151.21],['Cairo',30.04,31.24],['Reykjavik',64.15,-21.94],['Nairobi',-1.29,36.82],['Sào Paulo',-23.55,-46.63],['Singapore',1.35,103.82],['Mumbai',19.08,72.88],['Honolulu',21.31,-157.86]];
function buildPinGlobe(){
  const pg=$('pgPins');if(!pg)return;
  pg.innerHTML=PINS.map(c=>{
    const lat=c[1],lon=c[2];
    const l=clamp(50+(lon/160)*44,8,92),t=clamp(50-(Math.abs(lat)/85)*46,8,92);
    return `<div class="pg-pin" style="left:${l.toFixed(1)}%;top:${t.toFixed(1)}%" onclick="pinGo('${c[0]}')" title="${c[0]}"><span class="pg-tip">${c[0]}</span></div>`;
  }).join('');
}
function pinGo(name){$('searchInput').value=name;$('autocomplete').classList.remove('show');search()}
buildPinGlobe();

/* ================= error/load ================= */
function showLoad(){
  $('loaderOverlay').classList.add('show');
  let p=0;const pr=$('loadPct');
  clearInterval(window._ld);
  window._ld=setInterval(()=>{p+=Math.floor(rand(2,9));if(p>99)p=99;pr.textContent=String(p).padStart(2,'0')+'%'},150);
  setTimeout(()=>{clearInterval(window._ld);pr.textContent='99%'},2300);
}
function hideLoad(){
  clearInterval(window._ld);$('loadPct').textContent='100%';
  setTimeout(()=>{const o=$('loaderOverlay');o.classList.remove('show');o.querySelector('.loader-percent').textContent='00%'},320);
}
function showError(msg){hideLoad();$('errorText').textContent=msg;$('errorCard').classList.add('show')}

/* ================= the main event ================= */
let lastPing=0;
async function go(city){
  showLoad();
  $('errorCard').classList.remove('show');
  const t0=performance.now();
  try{
    const lat=city.latitude,lon=city.longitude,tz=city.timezone||'auto';
    const url=`https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current=temperature_2m,relative_humidity_2m,apparent_temperature,weather_code,wind_speed_10m,wind_direction_10m,wind_gusts_10m,is_day,visibility,uv_index,surface_pressure&hourly=temperature_2m,weather_code,is_day&daily=weather_code,temperature_2m_max,temperature_2m_min,sunrise,sunset&timezone=${encodeURIComponent(tz)}&forecast_days=7`;
    const data=await (await fetch(url)).json();
    lastPing=Math.max(12,Math.round(performance.now()-t0));
    $('latV').textContent=lastPing+' ms';
    weather=data;geo=city;
    render();
    hideLoad();
    saveRecent(city);
    typeLine($('typedCmd'),`./skypulse --city "${city.name||'random'}" --unit ${UNIT}  ✔ 0 errors`,14);
  }catch(e){hideLoad();showError('$ net :: ECONNRESET — satellites unreachable, retry')}
}
function saveRecent(c){
  if(!c||!c.name)return;
  let rec=JSON.parse(localStorage.getItem('skp_recent')||'[]');
  rec=rec.filter(x=>x.name!==c.name);
  rec.unshift({name:c.name,lat:c.latitude,lon:c.longitude});
  rec=rec.slice(0,8);localStorage.setItem('skp_recent',JSON.stringify(rec));
  renderTicker(rec);
}
function renderTicker(rec){
  const tk=$('ticker');
  if(!tk)return;
  if(!rec.length){tk.classList.remove('show');return}
  const chips=rec.map(c=>`<span class="tick" onclick="input.value='${c.name.replace(/'/g,'')}';search()">📍 ${c.name}</span>`).join('');
  $('tracker').innerHTML=chips+chips;
  tk.classList.add('show');
}

/* ================= render ================= */
function render(){
  const c=weather.current,dt=weather.daily;
  const isNight=c.is_day===0;
  const cond=condOf(c.weather_code);
  const zoneKey=pickZone(geo.latitude,c.temperature_2m,c.relative_humidity_2m,cond);
  applyTheme(zoneKey,cond,isNight);

  const tc=tempColor(c.temperature_2m);
  document.documentElement.style.setProperty('--tempc',tc);
  const gt=$('globeTemp');if(gt)gt.textContent=fmtT(c.temperature_2m)+'°';
  $('barTemp').textContent=fmtT(c.temperature_2m)+uSym();

  // drop a pin on the globe at the city's real position (equirectangular face-map)
  const pin=$('gPin'),pinLbl=$('gPinLbl');
  if(pin&&geo.latitude!==undefined){
    pin.style.left=clamp(50+(geo.longitude/160)*44,10,90)+'%';
    pin.style.top=clamp(50-(Math.abs(geo.latitude)/85)*46,8,92)+'%';
    pin.classList.remove('show');void pin.offsetHeight;pin.classList.add('show');
    pinLbl.textContent=`📍 ${geo.name.split(',')[0].slice(0,18)} · ${Math.abs(geo.latitude).toFixed(1)}°${geo.latitude>=0?'N':'S'} ${Math.abs(geo.longitude).toFixed(1)}°${geo.longitude>=0?'E':'W'}`;
    pinLbl.classList.remove('show');void pinLbl.offsetHeight;pinLbl.classList.add('show');
  }

  // smooth city-switch wipe
  const w=$('wipe');w.classList.remove('on');void w.offsetWidth;w.classList.add('on');
  $('cmdLine').innerHTML=`<span class="p">└─$</span> <span id="typedCmd" class="dt"></span><span class="caret"></span>`;

  clearInterval(window._bolt);
  if(cond==='storm')window._bolt=setInterval(()=>{if(Math.random()>.6){$('lightning').classList.add('flash');setTimeout(()=>$('lightning').classList.remove('flash'),200)}},1500);

  const hs=$('hourlyScroll');hs.innerHTML='';
  const curH=new Date().getHours();
  let start=weather.hourly.time.findIndex(t=>new Date(t).getHours()===curH);
  if(start<0)start=curH;
  for(let i=0;i<24&&start+i<weather.hourly.time.length;i++){
    const h=new Date(weather.hourly.time[start+i]).getHours();
    const nd=condOf(weather.hourly.weather_code[start+i]);
    const card=document.createElement('div');card.className='hour-card'+(i===0?' h-now':'');card.style.animationDelay=(i*.04)+'s';
    const lab=i===0?'NOW':h===0?'12 AM':h<12?h+' AM':h===12?'12 PM':(h-12)+' PM';
    card.innerHTML=`<div class="h-time">${lab}</div><span class="h-icon">${CM[nd].icon[weather.hourly.is_day[start+i]?0:1]}</span><div class="h-temp">${fmtT(weather.hourly.temperature_2m[start+i])}°</div>`;
    hs.appendChild(card);
  }
  $('hourlySection').style.display='block';

  const dl=$('dailyList');dl.innerHTML='';
  const mx=Math.max(...dt.temperature_2m_max),mn=Math.min(...dt.temperature_2m_min),rg=(mx-mn)||1;
  for(let i=0;i<dt.time.length;i++){
    const d=new Date(dt.time[i]);
    const nm=d.toLocaleDateString('en-US',{weekday:'short'});
    const nd=condOf(dt.weather_code[i]);
    const lo=dt.temperature_2m_min[i],hi=dt.temperature_2m_max[i];
    const row=document.createElement('div');row.className='day-row';row.style.animationDelay=(i*.07)+'s';
    row.innerHTML=`<div class="d-name">${i===0?'TODAY':nm}${i===0?'<span class="d-now-tag"></span>':''}</div><div class="d-icon">${CM[nd].icon[0]}</div><div class="d-range"><div class="d-fill" style="--grow:${(hi-lo)/rg}"></div></div><div class="d-temps"><span class="d-low">${fmtT(lo)}°</span><span class="d-high">${fmtT(hi)}°</span></div>`;
    dl.appendChild(row);
  }
  $('dailySection').style.display='block';
  fillDaily();

  const strip=$('strip');
  strip.innerHTML=[
    `lat ${Math.abs(geo.latitude).toFixed(2)}°${geo.latitude>=0?'N':'S'}`,
    `zone ${zoneKey}`,
    `high ${fmtT(mx)}° / low ${fmtT(mn)}°`,
    `night ${isNight?'true':'false'}`
  ].map((x,i)=>`<div class="strip-item" style="animation-delay:${i*.05}s">$ ${x}</div>`).join('');

  // telemetry
  const tq=$('telemetry');
  const bars=(p)=>`<div class="bar-meter"><i style="width:${p}%"></i></div>`;
  tq.innerHTML=`
    <div class="tele-item"><div class="tele-ic">🛰️</div><div class="tele-k">sat_link</div><div class="tele-v">+${Math.round(c.wind_speed_10m*3)} kbps</div>${bars(70)}</div>
    <div class="tele-item"><div class="tele-ic">⚡</div><div class="tele-k">api_latency</div><div class="tele-v b"><span id="latV2">${lastPing||'--'}</span> ms</div>${bars(lastPing?Math.min(100,lastPing/2):30)}</div>
    <div class="tele-item"><div class="tele-ic">📡</div><div class="tele-k">elevation</div><div class="tele-v">${Math.abs(geo.latitude).toFixed(1)}°</div>${bars(Math.min(100,Math.abs(geo.latitude)))}</div>
    <div class="tele-item"><div class="tele-ic">🧪</div><div class="tele-k">forecast_ver</div><div class="tele-v b">open-meteo 1.x</div>${bars(88)}</div>
    <div class="tele-item"><div class="tele-ic">🗃️</div><div class="tele-k">cache</div><div class="tele-v">${(JSON.stringify(weather).length/1024).toFixed(0)} KB</div>${bars(50)}</div>
  `;

  const fxm={clear:isNight?'none':'dust',rain:'rain',snow:'snow',storm:'rain',cloudy:'dust',overcast:'dust',fog:'dust'};
  spawnFX(fxm[cond]);
  mcs.style.display=(cond==='rain'||cond==='snow')?'none':'block';
}
function setUnit(u){
  UNIT=u;
  $('btnC').style.background=u==='C'?'var(--acc)':'';$('btnC').style.color=u==='C'?'#000':'';
  $('btnF').style.background=u==='F'?'var(--acc)':'';$('btnF').style.color=u==='F'?'#000':'';
  if(weather)render();
}
$('searchBtn').addEventListener('click',()=>{ac.classList.remove('show');search()});
window.addEventListener('keydown',e=>{if(e.key==='/'&&document.activeElement!==input){e.preventDefault();input.focus();input.select()}});

/* ================= 3D tilt ================= */
document.querySelectorAll('.tilt').forEach(el=>{
  el.addEventListener('mousemove',e=>{
    const r=el.getBoundingClientRect();
    const x=(e.clientX-r.left)/r.width-.5,y=(e.clientY-r.top)/r.height-.5;
    el.style.transform=`rotateY(${x*7}deg) rotateX(${y*-7}deg)`;
  });
  el.addEventListener('mouseleave',()=>{el.style.transform=''});
});

/* ================= globe parallax ================= */
const gw=document.querySelector('.globe-wrap');
if(gw){
  document.querySelector('.glass-3d').addEventListener('mousemove',e=>{
    const r=document.querySelector('.glass-3d').getBoundingClientRect();
    const x=(e.clientX-r.left)/r.width-.5,y=(e.clientY-r.top)/r.height-.5;
    gw.style.setProperty('--gx',(y*-22+8)+'deg');
    gw.style.setProperty('--gy',(x*40-18)+'deg');
  });
  document.querySelector('.glass-3d').addEventListener('mouseleave',()=>{
    gw.style.setProperty('--gx','-10deg');gw.style.setProperty('--gy','18deg');
  });
}

/* ================= scroll reveal ================= */
const io=new IntersectionObserver(es=>es.forEach(e=>{if(e.isIntersecting){e.target.classList.add('visible');io.unobserve(e.target)}}),{threshold:.1,rootMargin:'0px 0px -60px 0px'});
function initReveal(){document.querySelectorAll('.scroll-fade:not(.visible)').forEach(el=>io.observe(el))}
initReveal();

/* stagger the 7-day bars only when their section is actually on screen */
/* stagger the 7-day bars only when their panel is actually on screen */
const dIo=new IntersectionObserver(es=>es.forEach(e=>{
  if(e.isIntersecting){
    [...e.target.querySelectorAll('.day-row')].forEach((r,i)=>setTimeout(()=>r.classList.add('in-view'),(i*90)+140));
    dIo.unobserve(e.target);
  }
}),{threshold:.1,rootMargin:'0px 0px -60px 0px'});
function fillDaily(){
  const p=$('dailySection');if(!p)return;
  [...p.querySelectorAll('.day-row')].forEach(r=>r.classList.remove('in-view'));
  dIo.observe(p);
}
fillDaily();

/* ================= apple-style cascade builder ================= */
function buildInit(){
  [...document.querySelectorAll('.hero-left > *')].forEach((el,i)=>{el.setAttribute('data-build','');el.style.setProperty('--d',(i*.09).toFixed(2)+'s')});
  [...document.querySelectorAll('.hero-grid > *')].forEach((el,i)=>{el.setAttribute('data-build','');el.style.setProperty('--d',(i*.12).toFixed(2)+'s')});
  document.querySelectorAll('.section-head').forEach((el,i)=>{el.setAttribute('data-build','');el.style.setProperty('--d',(i*.08).toFixed(2)+'s')});
  document.querySelectorAll('.section .glass').forEach((el,i)=>{el.setAttribute('data-build','');el.style.setProperty('--d',((i*.09)+.08).toFixed(2)+'s')});
}
buildInit();
initReveal();
// safety: never leave above-the-fold sections hidden
setTimeout(()=>{document.querySelectorAll('.scroll-fade').forEach(el=>{if(el.getBoundingClientRect().top<innerHeight*.9)el.classList.add('visible')})},650);

/* ================= scroll scenery (parallax) ================= */
const shardLayer=document.querySelector('.glass-shards'),mCanvas=$('matrix'),hintEl=document.querySelector('.scroll-hint');
let scrollQueued=false;
function onScroll(){
  if(shardLayer)shardLayer.style.transform=`translate3d(0,${scrollY*-0.1}px,0)`;
  if(mCanvas)mCanvas.style.transform=`translate3d(0,${scrollY*0.06}px,0)`;
  if(hintEl)hintEl.style.opacity=scrollY<40?1:0;
  scrollQueued=false;
}
function scrollTick(){if(!scrollQueued){scrollQueued=true;requestAnimationFrame(onScroll)}}
addEventListener('scroll',scrollTick,{passive:true});
onScroll();

/* recent ticker on boot */
renderTicker(JSON.parse(localStorage.getItem('skp_recent')||'[]'));
</script>
</body>
</html>
