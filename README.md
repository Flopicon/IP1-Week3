# Week 4 practice: Vue components and reactivity

Campus Events, the reference app we build together in practice sessions.
This week it runs on hard-coded data (`src/data/events.ts`).

Requires Node 24 LTS (npm 11).

```bash
npm install
npm run dev     # open http://localhost:5173
npm test        # component tests (Vitest + Vue Test Utils)
npm run check   # type-check .ts and .vue files
```

Work through the TODOs in this order:

1. `src/components/EventCard.vue`: props, emits, computed, v-if, class and attribute bindings
2. `src/components/EventList.vue`: v-model, computed filtering, v-for with keys, passing events up
3. `src/App.vue`: state that changes when you book; bonus: watch + localStorage

You are done when `npm test` passes (15 tests) and `npm run check` shows no errors.
The last App test ("keeps my bookings after a reload") is the bonus.

# Q and A
1. In EventList, what goes wrong if you use the array index as :key and the list gets filtered while a card has local state?

Using the array index as a key can cause Vue to reuse the wrong component when the list changes. For example, if I filter out the first event, another event might reuse its component, so its local state could appear on the wrong card. Using event.id as the key helps Vue identify each event correctly.

2. Why is visible a computed and not a function called from the template? What is the difference when you type in the search box?

visible is a computed property because its value depends on reactive data, such as search and category. Vue updates it when those values change and caches the result until its dependencies change. A normal function called from the template can run again whenever Vue re-renders the component.

3. EventCard emits book instead of changing event.booked. Give two reasons.

First, props should not be changed directly by a child component because the parent owns the data. Second, emitting a book event lets the parent handle the booking, check whether seats are available, and update the event data in one place.

4. You write `const { booked } = reactive(event); booked++. The page does not change. Why?

Destructuring booked from a reactive object creates a separate local variable instead of keeping the reactive connection to the original property. Changing that variable does not update event.booked, so Vue does not detect the expected change. I should update the property through the reactive object instead.

5. Where does myBookings live, and why not in EventCard or EventList?

myBookings lives in App.vue because it tracks all the bookings made by the user across the application. EventCard displays one event, and EventList displays and filters multiple events, so neither is the best place to manage the overall booking list. Keeping the state in the parent lets it update the booking count and handle bookings in one place.