# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: iteration4.spec.js >> test mode exposes all bosses without touching normal save or personal best
- Location: e2e\iteration4.spec.js:44:1

# Error details

```
Error: expect(received).toEqual(expected) // deep equality

- Expected  - 1
+ Received  + 1

  Object {
    "best": "{\"version\":2,\"time\":432}",
-   "save": "{\"version\":2,\"rank\":0,\"floor\":0,\"rep\":[37,0,0,0,0],\"completed\":[],\"energy\":100,\"bathroom\":0,\"position\":{\"x\":0,\"z\":16},\"elapsed\":0.062099999994039534,\"bossesDefeated\":0,\"legacy\":false,\"result\":null,\"collectibles\":[\"collectible:0:fixture\"],\"officeLife\":{\"wait\":64.8831,\"clock\":0.1169000000000001,\"cooldowns\":{},\"active\":null,\"outcomes\":{},\"cvPending\":null,\"emergency\":null,\"gameOver\":null}}",
+   "save": "{\"version\":2,\"rank\":0,\"floor\":0,\"rep\":[37,0,0,0,0],\"completed\":[],\"energy\":100,\"bathroom\":0,\"position\":{\"x\":0,\"z\":16},\"elapsed\":0.23509999999403952,\"bossesDefeated\":0,\"legacy\":false,\"result\":null,\"collectibles\":[\"collectible:0:fixture\"],\"officeLife\":{\"wait\":64.71669999999999,\"clock\":0.2833000000000002,\"cooldowns\":{},\"active\":null,\"outcomes\":{},\"cvPending\":null,\"emergency\":null,\"gameOver\":null}}",
  }
```

# Page snapshot

```yaml
- generic [active]:
  - generic "3D office game" [ref=e1]
  - generic:
    - banner [ref=e2]:
      - link "Workdayle home" [ref=e3] [cursor=pointer]:
        - /url: "#"
        - generic [ref=e4]: w.
        - text: workdayle
      - generic [ref=e6]:
        - text: SOPRA STERIA
        - generic [ref=e8]: /
        - text: STAVANGER
        - generic [ref=e9]: OFFICE HOURS
      - generic "Career completion timer" [ref=e10]:
        - generic [ref=e11]: TIME
        - strong [ref=e12]: 00:00
        - generic [ref=e13]: PB --:--
      - generic [ref=e14]:
        - button "Hide stats" [expanded] [ref=e15] [cursor=pointer]
        - button "Test mode" [ref=e16] [cursor=pointer]
        - button "Mute audio" [ref=e17] [cursor=pointer]
        - button "Pause game" [ref=e20] [cursor=pointer]
    - complementary [ref=e23]:
      - generic [ref=e24]:
        - text: EMPLOYEE DASHBOARD
        - generic [ref=e25]: "01"
      - generic [ref=e31]:
        - text: PROBABLY BILLABLE
        - heading "Consultant" [level=2] [ref=e32]
        - text: "Employee #0001 / You"
      - generic [ref=e33]:
        - generic [ref=e35]:
          - generic [ref=e38]: Health
          - strong [ref=e39]: "100"
        - generic [ref=e43]:
          - generic [ref=e46]: Energy
          - strong [ref=e47]: "100"
        - generic [ref=e51]:
          - generic [ref=e54]: Bathroom need
          - strong [ref=e55]: "0"
        - paragraph [ref=e57]: All systems nominal. Suspicious.
      - generic [ref=e58]:
        - generic [ref=e59]: YOUR REPUTATION
        - generic [ref=e63]:
          - strong [ref=e64]: "37"
          - generic [ref=e65]: / 125 REP
        - paragraph [ref=e68]: A little effort. A lot of visibility.
      - generic [ref=e69]: "COLLECTIBLES: 1 / 24"
      - generic [ref=e70]:
        - generic [ref=e71]: THE CORPORATE LADDER
        - generic [ref=e72]:
          - generic [ref=e73]: "01"
          - generic [ref=e74]: Consultant
          - generic [ref=e75]: YOU
        - generic [ref=e76]:
          - generic [ref=e77]: "02"
          - generic [ref=e78]: Office Manager
        - generic [ref=e79]:
          - generic [ref=e80]: "03"
          - generic [ref=e81]: Department Manager
        - generic [ref=e82]:
          - generic [ref=e83]: "04"
          - generic [ref=e84]: Executive Director
        - generic [ref=e85]: CEO
      - generic [ref=e90]:
        - text: "TEST MODE: saving disabled"
        - paragraph [ref=e92]: A career is just a side quest with rent.
    - main:
      - generic:
        - text: FLOOR 01
        - heading "The delivery floor." [level=1]
        - paragraph: Where the actual work happens.
      - generic:
        - generic:
          - text: TODAY'S AMBITION
          - heading "Make yourself useful." [level=3]
          - paragraph: Complete colleague tasks. Earn 125 REP to challenge your boss.
        - generic: "01"
      - generic:
        - strong: STAVANGER HQ
        - generic: FIND YOUR WAY. FOLLOW THE ROOM SIGNS.
      - generic:
        - generic:
          - generic: W
          - generic: A
          - generic: S
          - generic: D
          - generic: Move
          - generic: E
          - generic: Interact
          - generic: SHIFT
          - generic: Sprint
        - generic: AVAILABLE TASK 0 / 16 DONE
    - status [ref=e93]: Normal career restored. Test progress was discarded.
```

# Test source

```ts
  1   | import { test, expect } from '@playwright/test';
  2   | 
  3   | test.setTimeout(60000);
  4   | 
  5   | async function boot(page) {
  6   |   await page.goto('/');
  7   |   await page.locator('#begin').click();
  8   | }
  9   | 
  10  | async function interact(page, id) {
  11  |   await page.evaluate(async id => {
  12  |     const { walkTo } = await import('/test-support/walk-bot.js');
  13  |     walkTo(window.__workdayle, id);
  14  |   }, id);
  15  |   await page.keyboard.press('KeyE');
  16  | }
  17  | 
  18  | test('stats toggle reclaims viewport and does not trap dialog keyboard focus', async ({ page }) => {
  19  |   await boot(page);
  20  |   const before = await page.locator('#game').boundingBox();
  21  |   await page.locator('#stats-toggle').click();
  22  |   await expect(page.locator('#sidebar')).toBeHidden();
  23  |   await expect(page.locator('#stats-toggle')).toHaveAttribute('aria-expanded', 'false');
  24  |   const after = await page.locator('#game').boundingBox();
  25  |   expect(after.width).toBe(before.width + before.x);
  26  |   expect(after.x).toBe(0);
  27  |   await page.keyboard.press('KeyM');
  28  |   await expect(page.locator('#sidebar')).toBeVisible();
  29  |   await page.locator('#pause').click();
  30  |   await page.keyboard.press('Tab');
  31  |   await expect(page.locator('#restart')).toBeFocused();
  32  |   await expect(page.locator('#sidebar')).toBeVisible();
  33  |   await page.locator('#resume').click();
  34  |   await interact(page, 'elevator');
  35  |   await page.keyboard.press('F2');
  36  |   await page.locator('#cancel-test').click();
  37  |   await expect(page.locator('[data-floor="1"]')).toBeDisabled();
  38  |   await page.locator('#leave').click();
  39  |   await page.setViewportSize({ width: 650, height: 850 });
  40  |   await page.locator('#stats-toggle').click();
  41  |   expect((await page.locator('#game').boundingBox()).width).toBe(650);
  42  | });
  43  | 
  44  | test('test mode exposes all bosses without touching normal save or personal best', async ({ page }) => {
  45  |   await boot(page);
  46  |   const original = await page.evaluate(() => {
  47  |     const game = window.__workdayle;
  48  |     game.state.rep[0] = 37;
  49  |     game.state.collectibles.add('collectible:0:fixture');
  50  |     game.save();
  51  |     localStorage.setItem('workdayle-best-v2', JSON.stringify({ version: 2, time: 432 }));
  52  |     return { save: localStorage.getItem('workdayle-save-v1'), best: localStorage.getItem('workdayle-best-v2') };
  53  |   });
  54  |   await page.locator('#test-menu').click();
  55  |   await page.locator('#enable-test').click();
  56  |   await expect(page.locator('#test-mode-badge')).toBeVisible();
  57  |   for (let floor = 0; floor < 4; floor++) {
  58  |     await page.keyboard.press('F2');
  59  |     await page.locator(`[data-test-boss="${floor}"]`).click();
  60  |     await page.waitForFunction(() => window.__workdayle.mode === 'combat');
  61  |     expect(await page.evaluate(() => window.__workdayle.state.bossReady)).toBe(true);
  62  |     expect(await page.evaluate(() => window.__workdayle.arena.bossMesh.userData.headSurface.group.userData.photoState)).toBe('ready');
  63  |     await page.evaluate(() => window.__workdayle.save());
  64  |   }
  65  |   await page.evaluate(() => window.__workdayle.combat.finish('win'));
  66  |   await page.locator('#skip-cinematic').click();
  67  |   await expect(page.locator('.corporate-crawl')).toBeVisible();
  68  |   expect(await page.evaluate(() => localStorage.getItem('workdayle-best-v2'))).toBe(original.best);
  69  |   await page.keyboard.press('F2');
  70  |   await page.locator('#test-reset').click();
  71  |   await page.keyboard.press('F2');
  72  |   await page.locator('#disable-test').click();
  73  |   await expect(page.locator('#test-mode-badge')).toBeHidden();
  74  |   expect(await page.evaluate(() => window.__workdayle.state.rep[0])).toBe(37);
  75  |   expect(await page.evaluate(() => ({
  76  |     save: localStorage.getItem('workdayle-save-v1'), best: localStorage.getItem('workdayle-best-v2'),
> 77  |   }))).toEqual(original);
      |        ^ Error: expect(received).toEqual(expected) // deep equality
  78  | });
  79  | 
  80  | test('emergency gives twenty real seconds, pauses, succeeds at the bathroom door and persists HR failure', async ({ page }, info) => {
  81  |   await boot(page);
  82  |   await page.evaluate(() => { window.__workdayle.state.bathroom = 100; window.__workdayle.life.update(0); });
  83  |   await expect(page.locator('#bathroom-emergency')).toBeVisible();
  84  |   expect(await page.evaluate(() => window.__workdayle.state.officeLife.emergency)).toBeGreaterThan(18);
  85  |   await page.locator('#pause').click();
  86  |   const remaining = await page.evaluate(() => window.__workdayle.state.officeLife.emergency);
  87  |   await page.waitForTimeout(300);
  88  |   expect(await page.evaluate(() => window.__workdayle.state.officeLife.emergency)).toBe(remaining);
  89  |   await page.locator('#resume').click();
  90  |   await interact(page, 'bathroom');
  91  |   await expect(page.locator('#bathroom-vignette')).toBeHidden();
  92  |   expect(await page.evaluate(() => window.__workdayle.state.bathroom)).toBe(0);
  93  |   await interact(page, 'bathroom-exit');
  94  |   await page.evaluate(() => {
  95  |     const game = window.__workdayle;
  96  |     game.state.bathroom = 100;
  97  |     game.life.update(0);
  98  |     game.life.update(20);
  99  |   });
  100 |   await expect(page.locator('#modal-title')).toHaveText('GAME OVER');
  101 |   await expect(page.locator('#modal')).toContainText('HR HAS BEEN NOTIFIED');
  102 |   await page.screenshot({ path: info.outputPath('hr-game-over.png') });
  103 |   await page.reload();
  104 |   await page.locator('#begin').click();
  105 |   await expect(page.locator('#modal-title')).toHaveText('GAME OVER');
  106 |   await page.locator('#restart-career').click();
  107 |   expect(await page.evaluate(() => window.__workdayle.state.bathroom)).toBe(0);
  108 |   await expect(page.locator('#bathroom-emergency')).toBeHidden();
  109 | });
  110 | 
  111 | test('recruitment choices have distinct consequences and a CV warning leads to an AI workstation task', async ({ page }) => {
  112 |   await boot(page);
  113 |   await page.evaluate(() => window.__workdayle.life.present('recruitment'));
  114 |   await page.locator('#reject-offer').click();
  115 |   expect(await page.evaluate(() => window.__workdayle.state.rep[0])).toBe(15);
  116 |   await page.evaluate(() => window.__workdayle.life.present('cv-warning'));
  117 |   await page.locator('#cv-acknowledge').click();
  118 |   await expect(page.locator('#objective-title')).toHaveText('CV STATUS: YELLOW.');
  119 |   await interact(page, 'cv-workstation');
  120 |   await page.evaluate(async () => {
  121 |     const { solveMiniGame } = await import('/test-support/minigame-bot.js');
  122 |     solveMiniGame(window.__workdayle.mini);
  123 |   });
  124 |   expect(await page.evaluate(() => ({ pending: window.__workdayle.state.officeLife.cvPending, rep: window.__workdayle.state.rep[0] }))).toEqual({ pending: null, rep: 35 });
  125 |   await page.evaluate(() => window.__workdayle.life.present('recruitment'));
  126 |   await page.locator('#accept-offer').click();
  127 |   await expect(page.locator('#modal-title')).toHaveText('GAME OVER');
  128 |   expect(await page.evaluate(() => window.__workdayle.state.officeLife.outcomes.recruitment)).toBe('failed');
  129 | });
  130 | 
  131 | test('Jill snack introduction and all four victories show physical travel with safe skips', async ({ page }, info) => {
  132 |   test.setTimeout(90000);
  133 |   await boot(page);
  134 |   await page.evaluate(() => { window.__workdayle.state.rep[0] = 125; });
  135 |   await interact(page, 'boss');
  136 |   await page.locator('#fight').click();
  137 |   await expect(page.locator('#cinematic-title')).toContainText("JILL'S SNACK");
  138 |   await page.waitForFunction(() => window.__workdayle.cutscene.elapsed > 2);
  139 |   await expect(page.locator('#cinematic-line')).toContainText('MY domain');
  140 |   await page.screenshot({ path: info.outputPath('jill-snack.png') });
  141 |   await page.locator('#pause-cinematic').click();
  142 |   const elapsed = await page.evaluate(() => window.__workdayle.cutscene.elapsed);
  143 |   await page.waitForTimeout(200);
  144 |   expect(await page.evaluate(() => window.__workdayle.cutscene.elapsed)).toBe(elapsed);
  145 |   await page.locator('#resume').click();
  146 |   await page.locator('#skip-cinematic').click();
  147 |   await page.waitForFunction(() => window.__workdayle.mode === 'combat');
  148 |   for (let floor = 0; floor < 4; floor++) {
  149 |     await page.evaluate(async floor => {
  150 |       const game = window.__workdayle;
  151 |       if (floor > 0) {
  152 |         game.state.rep[floor] = 1000;
  153 |         game.startCombat();
  154 |         await game.arena.bossMesh.userData.headSurface.ready;
  155 |         game.setMode('combat');
  156 |       }
  157 |       game.combat.finish('win');
  158 |     }, floor);
  159 |     await expect(page.locator('#cinematic-title')).toContainText('DEFEATED');
  160 |     const promoted = await page.evaluate(() => window.__workdayle.state.rank);
  161 |     expect(promoted).toBe(floor + 1);
  162 |     await page.evaluate(() => window.__workdayle.cutscene.update(4.5));
  163 |     expect(await page.evaluate(() => window.__workdayle.cutscene.room.group.name)).toBe('promotion-lift-hall');
  164 |     await page.screenshot({ path: info.outputPath(`promotion-${floor}.png`) });
  165 |     await page.locator('#skip-cinematic').click();
  166 |     expect(await page.evaluate(() => window.__workdayle.mode)).toBe(floor < 3 ? 'office' : 'ending');
  167 |     if (floor < 3) expect(await page.evaluate(() => window.__workdayle.state.floor)).toBe(floor + 1);
  168 |   }
  169 |   await expect(page.locator('.corporate-crawl')).toBeVisible();
  170 | });
  171 | 
  172 | test('collectibles, hidden room and bathroom camera are optional and persist once', async ({ page }) => {
  173 |   await boot(page);
  174 |   const collectible = await page.evaluate(() => window.__workdayle.office.interactables.find(item => item.kind === 'collectible').id);
  175 |   await interact(page, collectible);
  176 |   const size = await page.evaluate(() => window.__workdayle.state.collectibles.size);
  177 |   expect(size).toBe(1);
```