# Banner generator (source of truth). Usage: python3 build_banner.py  -> dark.svg, light.svg (+ preview PNGs)
import numpy as np, random, sys
random.seed(7); np.random.seed(7)

CW=8.4   # monospace advance at 14px (0.6em)
INFO=[
 ("Subject","Vishnu Babalsure"),("Role","AI/ML & Data Science Developer"),("Origin","Pune, India"),
 ("Education","B.E. AI & Data Science (SPPU)"),("Status","Building + Learning + Shipping"),
 ("ToolChain","VS Code · PyCharm · IntelliJ · Colab · Power BI · Git"),None,
 ("Core.Lang","Python, C"),("Core.Frontend","React · Next.js · TypeScript"),
 ("Core.Backend","FastAPI · Node.js · Spring Boot"),("Core.Database","PostgreSQL · MongoDB"),
 ("Core.Infra","Vercel · GitHub Actions"),None,
 ("Grid.Mail","vishnubabalsure@gmail.com"),("Grid.Portfolio","coming soon"),
 ("Grid.LinkedIn","linkedin.com/in/vishnubabalsure"),("Grid.GitHub","github.com/Vishnubabalsure23-star"),
 ("Grid.Kaggle","kaggle.com/vishnubabalsure"),
]
HANDLE="@Vishnubabalsure23-star"
TITLE="vishnu@github ~ % ./profile.sh --live"

THEMES={
 'dark': dict(bg1='#0C1426',bg2='#0A101F',bar='#0B1222',frame_fill='#080D19',dot='#A78BFA',chrome='#22D3EE',
              label='#94A3B8',value='#F1F5F9',lead='#475569',title='#94A3B8',muted='#64748B',stroke='#1E293B',
              pill1='#7C3AED',pill2='#0891B2',pilltxt='#FFFFFF',tag='#A78BFA',tag2='#10B981',cap='#64748B'),
 'light':dict(bg1='#F8FAFC',bg2='#EEF2F7',bar='#F1F5F9',frame_fill='#FAFBFD',dot='#7C3AED',chrome='#0891B2',
              label='#64748B',value='#0F172A',lead='#94A3B8',title='#475569',muted='#94A3B8',stroke='#E2E8F0',
              pill1='#7C3AED',pill2='#0891B2',pilltxt='#FFFFFF',tag='#7C3AED',tag2='#059669',cap='#94A3B8'),
}
FX,FY,FW,FH=36,84,400,492      # portrait frame
GRID_W,GRID_H=300,369
S=FW/GRID_W

def esc(s): return s.replace('&','&amp;').replace('<','&lt;')

def groups_for(dots,n=60):
    ys,xs=np.nonzero(dots); idx=np.random.permutation(len(xs)); g=idx%n
    return ys,xs,g

def evenness(ys,xs,g,n=60,blk=15):
    # visible fraction per block when half the groups are revealed; ~0.05 = shimmer, ~0.7 = patchy
    vis=g<n//2; bw=GRID_W//blk; bh=GRID_H//blk+1
    tot=np.zeros((bh,bw)); v=np.zeros((bh,bw))
    for y,x,s in zip(ys,xs,vis):
        by,bx=min(y//blk,bh-1),min(x//blk,bw-1); tot[by,bx]+=1; v[by,bx]+=s
    ok=tot>=30
    return float(np.std(v[ok]/tot[ok]))

def build(mode,dots,preview=False):
    t=THEMES[mode]; ys,xs,g=groups_for(dots); ev=evenness(ys,xs,g)
    o=[]; A=o.append
    A(f'<svg xmlns="http://www.w3.org/2000/svg" width="1180" height="610" viewBox="0 0 1180 610" '
      f'font-family="ui-monospace,SFMono-Regular,Menlo,Consolas,\'Liberation Mono\',monospace" role="img" aria-label="Vishnu Babalsure - profile">')
    A(f'<defs><linearGradient id="bg" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="{t["bg1"]}"/><stop offset="1" stop-color="{t["bg2"]}"/></linearGradient>'
      f'<linearGradient id="bd" x1="0" y1="0" x2="1" y2="0"><stop offset="0" stop-color="#3B82F6"/><stop offset=".5" stop-color="#22D3EE"/><stop offset="1" stop-color="#10B981"/></linearGradient>'
      f'<linearGradient id="pill" x1="0" y1="0" x2="1" y2="0"><stop offset="0" stop-color="{t["pill1"]}"/><stop offset="1" stop-color="{t["pill2"]}"/></linearGradient>'
      f'<clipPath id="win"><rect x="3" y="3" width="1174" height="604" rx="16"/></clipPath>'
      f'<clipPath id="fr"><rect x="{FX}" y="{FY}" width="{FW}" height="{FH}" rx="10"/></clipPath></defs>')
    A(f'<rect x="3" y="3" width="1174" height="604" rx="16" fill="url(#bg)"/>')
    A(f'<g clip-path="url(#win)"><rect x="3" y="3" width="1174" height="44" fill="{t["bar"]}"/><line x1="3" y1="47" x2="1177" y2="47" stroke="{t["stroke"]}"/></g>')
    for i,c in enumerate(('#FF5F57','#FEBC2E','#28C840')): A(f'<circle cx="{30+i*20}" cy="25" r="6" fill="{c}"/>')
    tl=len(TITLE)*CW*13/14
    A(f'<text x="{590-tl/2:.1f}" y="29" font-size="13" fill="{t["title"]}" textLength="{tl:.1f}" lengthAdjust="spacingAndGlyphs">{esc(TITLE)}</text>')
    A(f'<rect x="3" y="3" width="1174" height="604" rx="16" fill="none" stroke="url(#bd)" stroke-width="2.5"/>')
    # portrait
    A(f'<text x="{FX+2}" y="70" font-size="10" letter-spacing="3" fill="{t["cap"]}">VISUAL.MAP</text>')
    A(f'<rect x="{FX}" y="{FY}" width="{FW}" height="{FH}" rx="10" fill="{t["frame_fill"]}" stroke="{t["chrome"]}" stroke-opacity=".45"/>')
    A(f'<g clip-path="url(#fr)"><g transform="translate({FX},{FY}) scale({S:.5f})" fill="{t["dot"]}" shape-rendering="crispEdges">')
    for k in range(60):
        sel=g==k; d=''.join(f'M{x} {y}h1v1h-1z' for y,x in zip(ys[sel],xs[sel]))
        if preview: A(f'<path d="{d}"/>')
        else: A(f'<path opacity="0" d="{d}"><animate attributeName="opacity" from="0" to="1" dur="0.45s" begin="{0.15+k*0.033:.3f}s" fill="freeze"/></path>')
    A('</g></g>')
    for (cx,cy,dx,dy) in ((FX,FY,1,1),(FX+FW,FY,-1,1),(FX,FY+FH,1,-1),(FX+FW,FY+FH,-1,-1)):
        A(f'<path d="M{cx+dx*16} {cy}H{cx}V{cy+dy*16}" fill="none" stroke="{t["chrome"]}" stroke-width="2"/>')
    # info panel
    X0,X1=470,1135
    A(f'<text x="{X0}" y="102" font-size="12" letter-spacing="3" fill="{t["chrome"]}">SYSTEM.INFO</text>')
    A(f'<line x1="{X0+130}" y1="98" x2="1050" y2="98" stroke="{t["stroke"]}" stroke-width="1.5"/>')
    A(f'<circle cx="1080" cy="97" r="4" fill="#EF4444"><animate attributeName="opacity" values="1;.25;1" dur="1.6s" repeatCount="indefinite"/></circle>'
      f'<text x="1089" y="102" font-size="12" font-weight="700" fill="#EF4444" textLength="{3*CW*12/14:.1f}" lengthAdjust="spacingAndGlyphs">LIVE</text>')
    pw=len(HANDLE)*CW+28
    A(f'<rect x="{X0}" y="118" width="{pw:.1f}" height="26" rx="13" fill="url(#pill)"/>'
      f'<text x="{X0+14}" y="136" font-size="14" font-weight="700" fill="{t["pilltxt"]}" textLength="{len(HANDLE)*CW:.1f}" lengthAdjust="spacingAndGlyphs">{esc(HANDLE)}</text>')
    y=176; ri=0; worst=1e9
    for row in INFO:
        if row is None: y+=14; continue
        lab,val=row; lw=len(lab)*CW; vw=len(val)*CW; vx=X1-vw
        n=int((vx-8-(X0+lw+8))//CW); worst=min(worst,n)
        dots='.'*max(n,0); b=f'begin="{0.5+ri*0.07:.2f}s"'
        pre=lab.split('.')[0] if '.' in lab else None
        lc=t['tag'] if pre=='Core' else t['tag2'] if pre=='Grid' else t['label']
        A(f'<g opacity="0"><animate attributeName="opacity" from="0" to="1" dur="0.4s" {b} fill="freeze"/>'
          f'<text x="{X0}" y="{y}" font-size="14" fill="{lc}" textLength="{lw:.1f}" lengthAdjust="spacingAndGlyphs">{esc(lab)}</text>'
          f'<text x="{X0+lw+8:.1f}" y="{y}" font-size="14" fill="{t["lead"]}" textLength="{len(dots)*CW:.1f}" lengthAdjust="spacingAndGlyphs">{dots}</text>'
          f'<text x="{vx:.1f}" y="{y}" font-size="14" fill="{t["value"]}" textLength="{vw:.1f}" lengthAdjust="spacingAndGlyphs">{esc(val)}</text></g>')
        y+=23; ri+=1
    A('</svg>')
    svg=''.join(o)
    if preview: svg=svg.replace('<g opacity="0">','<g opacity="1">')
    return svg,ev,worst,y

if __name__=='__main__':
    import cairosvg
    for mode in ('dark','light'):
        dots=np.load(f'dots_{mode}.npy')
        svg,ev,worst,yend=build(mode,dots)
        open(f'{mode}.svg','w').write(svg)
        pv,_,_,_=build(mode,dots,preview=True)
        cairosvg.svg2png(bytestring=pv.encode(),write_to=f'banner_{mode}.png',output_width=1180)
        print(mode,f'{len(svg)/1024:.0f}KB','evenness',round(ev,3),'min leader dots',worst,'last row y',yend)
