# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: tests\regression\specificRecordPreview.csr.spec.ts >> Listing Preview >> Specific Outstanding Parts Listing Preview
- Location: tests\regression\specificRecordPreview.csr.spec.ts:220:7

# Error details

```
Error: locator.waitFor: Target page, context or browser has been closed
Call log:
  - waiting for locator('div[name="headerMoreButtons"]') to be visible

```

# Test source

```ts
  284 |         const normalizedPdfText = pdfData.text.replace(/\s+/g, " ");
  285 |         const normalizedErrorText = errorText.replace(/\s+/g, " ");
  286 | 
  287 |         expect
  288 |           .soft(
  289 |             normalizedPdfText,
  290 |             `Error found in PDF: "${errorText}" should NOT be present`,
  291 |           )
  292 |           .not.toContain(normalizedErrorText);
  293 |       });
  294 |     }
  295 | 
  296 |     return response;
  297 |   }
  298 | 
  299 |   // Check First Row Checkbox and Open New Tab
  300 |   async checkFirstRowCheckbox(
  301 |     printButtonText: string = "Print Statement",
  302 |   ): Promise<Page> {
  303 |     let newTab!: Page;
  304 |     await step(`Check checkbox and open ${printButtonText}`, async () => {
  305 |       const table = this.page.getByRole("table").first();
  306 |       const firstDataRow = table
  307 |         .getByRole("row")
  308 |         .filter({ has: this.page.locator("td") })
  309 |         .first();
  310 | 
  311 |       await expect(firstDataRow).toBeVisible();
  312 |       const checkbox = firstDataRow.locator('input[type="checkbox"]').first();
  313 |       await checkbox.check();
  314 |       await expect(checkbox).toBeChecked();
  315 |       const printButton = this.page.locator("button.button.is-primary", {
  316 |         hasText: printButtonText,
  317 |       });
  318 |       const [tab] = await Promise.all([
  319 |         this.page.context().waitForEvent("page"),
  320 |         printButton.click(),
  321 |       ]);
  322 |       await tab.waitForLoadState("domcontentloaded");
  323 |       await expect(tab).toHaveURL(/\/v2\/printpreview\//);
  324 |       newTab = tab;
  325 |     });
  326 |     return newTab;
  327 |   }
  328 | 
  329 |   async uncheckFirstRowCheckbox(): Promise<void> {
  330 |     await step("Uncheck the first row checkbox", async () => {
  331 |       const table = this.page.getByRole("table").first();
  332 | 
  333 |       const firstDataRow = table
  334 |         .getByRole("row")
  335 |         .filter({ has: this.page.locator("td") })
  336 |         .first();
  337 | 
  338 |       await expect(firstDataRow).toBeVisible();
  339 |       const checkbox = firstDataRow.locator('input[type="checkbox"]').first();
  340 |       await expect(checkbox).toBeVisible();
  341 |       await checkbox.uncheck();
  342 |       await expect(checkbox).not.toBeChecked();
  343 |     });
  344 |   }
  345 | 
  346 |   //------------------------ Table First Row Interaction Methods ------------------------//
  347 |   // Method to open the first record from the table based on the provided URL
  348 |   async openFirstRecordFromTable(route: string): Promise<void> {
  349 |     await step(`Open first record from table on route: ${route}`, async () => {
  350 |       await this.page.waitForURL(`**${route}**`);
  351 | 
  352 |       const table = this.page.getByRole("table").first();
  353 | 
  354 |       const firstDataRow = table
  355 |         .getByRole("row")
  356 |         .filter({ has: this.page.locator("td") })
  357 |         .first();
  358 | 
  359 |       await expect(firstDataRow).toBeVisible();
  360 | 
  361 |       // Dynamically resolve the best clickable link
  362 |       const firstHrefLink = firstDataRow.locator("a[href]").first();
  363 |       const hasVisibleHref = await firstHrefLink.isVisible().catch(() => false);
  364 |       const link = hasVisibleHref
  365 |         ? firstHrefLink
  366 |         : firstDataRow.locator("a").first();
  367 | 
  368 |       await expect(link).toBeVisible();
  369 | 
  370 |       // Capture URL before click
  371 |       const urlBeforeClick = this.page.url();
  372 | 
  373 |       await link.click();
  374 | 
  375 |       // Wait until URL changes
  376 |       await this.page.waitForURL((url) => url.toString() !== urlBeforeClick, {
  377 |         waitUntil: "domcontentloaded",
  378 |       });
  379 | 
  380 |       // Wait for record page to be ready — any indicator
  381 |       await Promise.race([
  382 |         this.page
  383 |           .locator('div[name="headerMoreButtons"]')
> 384 |           .waitFor({ state: "visible" }),
      |            ^ Error: locator.waitFor: Target page, context or browser has been closed
  385 |         this.page
  386 |           .locator("a.button.is-primary.is-outlined.is-inverted")
  387 |           .waitFor({ state: "visible" }),
  388 |         this.page
  389 |           .locator("button.button.is-primary")
  390 |           .first()
  391 |           .waitFor({ state: "visible" }),
  392 |       ]);
  393 |     });
  394 |   }
  395 | 
  396 |   //----------------------- Common Methods ------------------------//
  397 |   // Method For Repairer Quote Listing Page to open Quote Analysis
  398 |   async openQuoteAnalysis() {
  399 |     await step("Click Quote Analysis", async () => {
  400 |       await expect(this.quoteAnalysis).toBeVisible();
  401 |       await this.quoteAnalysis.click();
  402 |     });
  403 | 
  404 |     await step("Clicking Print Button", async () => {
  405 |       await expect(this.printButton).toBeVisible();
  406 |       await this.printButton.click();
  407 |     });
  408 |   }
  409 | 
  410 |   // Method For Sales Analysis Listing Page
  411 |   async openSalesAnalysis() {
  412 |     await step("Click Sales Analysis", async () => {
  413 |       await expect(this.salesAnalysis).toBeVisible();
  414 |       await this.salesAnalysis.click();
  415 |     });
  416 | 
  417 |     await step("Clicking Print Button", async () => {
  418 |       await expect(this.printButton).toBeVisible();
  419 |       await this.printButton.click();
  420 |     });
  421 |   }
  422 | 
  423 |   // Ok Button Click Method in Print Preview
  424 |   async clickOkButton(opensNewTab: boolean = false): Promise<Page | undefined> {
  425 |     let newTab: Page | undefined;
  426 |     await step("Click Ok button", async () => {
  427 |       if (opensNewTab) {
  428 |         // Case 2: New tab
  429 |         const [tab] = await Promise.all([
  430 |           this.page.context().waitForEvent("page"),
  431 |           this.okButton.click(),
  432 |         ]);
  433 |         await tab.waitForLoadState("domcontentloaded");
  434 |         await expect(tab).toHaveURL(/printpreview/);
  435 |         newTab = tab;
  436 |       } else {
  437 |         // Case 1: Same tab
  438 |         await this.okButton.click();
  439 |       }
  440 |     });
  441 |     return newTab;
  442 |   }
  443 | 
  444 |   // Method Extracting the Quote Total
  445 |   parseCurrencyAmount(amountText: string): number {
  446 |     const amount = Number(amountText.replace(/[^0-9.-]/g, ""));
  447 |     if (Number.isNaN(amount)) {
  448 |       throw new Error(
  449 |         `Unable to parse currency amount from text: ${amountText}`,
  450 |       );
  451 |     }
  452 |     return Math.round((amount + Number.EPSILON) * 100) / 100;
  453 |   }
  454 | 
  455 |   parseXmlAmount(xml: string, tagName: string): number {
  456 |     const regex = new RegExp(`<${tagName}>\\s*([\\d.]+)\\s*<\\/${tagName}>`);
  457 |     const match = xml.match(regex);
  458 | 
  459 |     if (!match) {
  460 |       throw new Error(`${tagName} not found in XML.`);
  461 |     }
  462 |     const amount = Number(match[1]);
  463 |     if (Number.isNaN(amount)) {
  464 |       throw new Error(`Invalid amount for ${tagName}: ${match[1]}`);
  465 |     }
  466 |     return Math.round((amount + Number.EPSILON) * 100) / 100;
  467 |   }
  468 | 
  469 |   private quoteTotalAmountLocator(label: "Ex GST" | "Inc GST"): Locator {
  470 |     const labelText = `Total (${label})`;
  471 |     return this.page
  472 |       .locator(
  473 |         `div:has(> span.is-size-6.has-text-weight-semibold:has-text("${labelText}")) > span.has-text-success`,
  474 |       )
  475 |       .first();
  476 |   }
  477 | 
  478 |   async fetchQuoteTotal(): Promise<{
  479 |     totalExGstAmount: number;
  480 |     totalIncGstAmount: number;
  481 |   }> {
  482 |     return await step(
  483 |       "Fetch quote Total Ex GST and Total Inc GST from header",
  484 |       async () => {
```