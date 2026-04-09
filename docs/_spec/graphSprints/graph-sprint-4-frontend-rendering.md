# Graph Sprint 4: Frontend Chart Rendering

**References:** Letter2 Sections 3 (output schema), 9 (acceptance criteria)

**Depends on:** Graph Sprint 3 (GPT integration), Letter1 Sprint 6 (chat interface), Letter1 Sprint 7 (visualization groundwork)

---

## Sprint Goal

Render bar and pie charts inline in the chat interface using the structured data returned by `get_spending_chart_data`. After this sprint, the user asks for a chart via chat and sees a rendered visual alongside GPT's explanation.

---

## Implementation Tasks

1. **Install chart library (if not done in Letter1 Sprint 7)**
   - Recharts or Chart.js with a React wrapper
   - Confirm it supports both bar and pie chart types

2. **Build the chart rendering component**
   - Accept the tool's output schema as props: `chartType`, `labels`, `values`, `title`, `currency`, `total`, `highlights`
   - Render a bar chart when `chartType` is `"bar"`
   - Render a pie chart when `chartType` is `"pie"`
   - Display the `title` above the chart
   - Display the `total` formatted as currency below or beside the chart

3. **Implement cents-to-currency formatting**
   - Create a utility function: `formatCents(5000)` → `"$50.00"`
   - Use the `currency` field from the output to determine the symbol
   - Apply to: chart axis labels/tooltips, total display, highlights display

4. **Integrate chart into chat message flow**
   - Detect when the backend response contains chart data (result from `get_spending_chart_data`)
   - Render the chart component inline in the chat message area
   - Display GPT's natural-language explanation as text above or below the chart
   - Non-chart responses continue to render as text/structured data

5. **Implement highlights display**
   - When `highlights` is not null, render a summary label: e.g., "Highest: Groceries — $450.00"
   - When `highlights` is null (empty result), skip the highlights display

6. **Implement empty state**
   - When the tool returns empty data (labels: [], values: [], total: 0), display a message like "No spending data found for this period"
   - Do not render an empty chart

7. **Implement chart error handling**
   - If the chart library throws during rendering (e.g., malformed data), catch the error and display a user-friendly message
   - Do not crash the chat interface
   - Log the error for debugging

8. **Basic chart styling**
   - Ensure charts are readable at the size they render within the chat area
   - Reasonable colors for categories (use the chart library's default palette)
   - Tooltips on hover showing label and formatted value
   - No complex styling needed for MVP

---

## Expected Deliverables

- Chart rendering component (bar and pie)
- Cents-to-currency formatting utility
- Charts rendered inline in chat messages
- GPT explanation displayed alongside chart
- Highlights summary displayed when present
- Empty state handling (no empty charts)
- Chart error handling (no crashes)

---

## Dependencies

- Graph Sprint 3: GPT selects and invokes the chart tool, returns data to frontend
- Letter1 Sprint 6: chat interface with message rendering
- Letter1 Sprint 7: visualization framework (if chart library was installed there)

---

## Risk Areas

- Chart sizing: charts must fit within the chat message area without overflow. Set a max width/height and let the chart library handle responsive scaling within that.
- Pie chart with many categories: if there are 10+ categories, the pie chart may become hard to read. For MVP, this is acceptable — note it for future improvement (e.g., "Other" grouping).
- Double rendering: ensure the chart component doesn't re-render unnecessarily when the chat scrolls or new messages arrive. Use React.memo or equivalent if needed.

---

## Test Tasks

### Unit tests:
- Chart component renders a bar chart given valid bar data
- Chart component renders a pie chart given valid pie data
- `formatCents(5000)` returns `"$50.00"`
- `formatCents(0)` returns `"$0.00"`
- `formatCents(99)` returns `"$0.99"`
- Empty data (labels: [], values: []) → renders empty state message
- Highlights present → summary label rendered with formatted value
- Highlights null → no summary label rendered
- Malformed chart data → error message rendered, no crash
- Title displayed above chart
- Total displayed and formatted correctly

### Integration tests:
- Backend returns chart data → chat renders chart component inline
- Backend returns non-chart data → no chart rendered (text/structured data only)
- Backend returns chart data with GPT explanation → both chart and text displayed
- Backend returns empty chart data → empty state message displayed

### E2E tests:
- User types "Show me a pie chart of my spending this year" → pie chart renders with correct categories and values
- User types "Bar chart of monthly expenses for 2026" → bar chart renders with monthly labels
- User asks for chart with category filter → chart shows only filtered categories
- User asks for chart, then follows up asking to switch to pie → new chart renders
- User with no transactions asks for a chart → empty state message displayed
- User sends ambiguous chart request → clarification prompt → user responds → chart renders

---

## Done Criteria

- [ ] Bar chart renders correctly from tool output
- [ ] Pie chart renders correctly from tool output
- [ ] Charts displayed inline in chat messages
- [ ] GPT explanation text displayed alongside chart
- [ ] Values formatted from cents to currency
- [ ] Highlights displayed when present, hidden when null
- [ ] Empty state handled — message shown, no empty chart
- [ ] Chart errors handled — user-friendly message, no crashes
- [ ] All unit, integration, and E2E tests pass
