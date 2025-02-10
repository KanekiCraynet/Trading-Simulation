Below is the complete documentation for the Simulation Market-Making Game program. This documentation explains the overall logic flow, describes key features and code functions, and provides insights into how the HTML, CSS, and JavaScript components work together to create an interactive simulation. (Psst… it’s as lit as your favorite meme—just with more market jargon!)  

---

# Simulation Market-Making Game Documentation

## Table of Contents
1. [Overview](#overview)
2. [HTML Structure](#html-structure)
3. [CSS Styling](#css-styling)
4. [JavaScript Functionality](#javascript-functionality)  
   4.1. [Global State and Initialization](#global-state-and-initialization)  
   4.2. [Utility Functions](#utility-functions)  
   4.3. [Event Listeners](#event-listeners)  
   4.4. [Simulation Cycle and Animation](#simulation-cycle-and-animation)
5. [Logic Flow Summary](#logic-flow-summary)
6. [Conclusion](#conclusion)

---

## 1. Overview

The Simulation Market-Making Game is a web-based interactive application that simulates market-making dynamics with two roles: **Market Maker** and **Market Participant**. Players interact by submitting market orders (as makers) or executing buy/sell orders (as participants). The simulation uses dice rolls (with a twist) to determine outcomes, updates a cumulative Profit/Loss (P/L) chart in real time using Chart.js, and logs events for review.  

*In Gen Z lingo: This game is basically a trading simulator that’s as dynamic and unpredictable as your group chat drama!*

---

## 2. HTML Structure

The HTML document is structured into several key sections:

- **Header**:  
  Contains the title "Simulation Market-Making Game" and is styled to be sticky at the top. It also links to external resources (favicon, Google Fonts, and Chart.js).

- **Content Container**:  
  Encloses all game elements and includes:
  
  - **Chart Container**:  
    Holds a `<canvas>` element with the id `pl-chart` where the cumulative P/L is plotted via Chart.js.
  
  - **Price Table**:  
    A hidden table (by default) that shows the current bid and offer prices. This becomes visible when a user selects the Market Participant role.
  
  - **Quote Board and Progress Bar**:  
    Displays dice values (each dice value can be a number or an "H" for a hidden roll) and a progress bar indicating the cycle’s time remaining. It also shows the realization and P/L details once a cycle completes.
  
  - **Role Selection Buttons**:  
    Two buttons allow the user to choose their role as either Market Maker or Market Participant.
  
  - **Market Controls**:  
    Depending on the chosen role:
    - **Market Maker Controls**:  
      Input fields for setting the midpoint price and spread, plus a submit button.
    - **Market Participant Controls**:  
      Input for quantity and buttons for “Buy” and “Sell” actions.
  
  - **Simulation Settings**:  
    Includes a slider to adjust the cycle duration (simulation speed) and a button to reset the simulation.
  
  - **Log Panel**:  
    A scrolling panel that displays a log of simulation events (with timestamps).

---

## 3. CSS Styling

The CSS is designed to provide a dark, modern aesthetic with the following features:

- **Layout and Typography**:  
  Uses the Roboto font, flexbox for centering, and consistent padding/margins for a clean layout.
  
- **Color Scheme**:  
  The background, text, and interactive elements use a palette of dark tones with accent colors (e.g., teal `#76c7c0` and red `#dc3545` for alerts) that give the application a sleek look.
  
- **Responsive Design**:  
  The style rules are set up to work on various screen sizes (e.g., width constraints for containers, responsive progress bar).
  
- **Interactive Elements**:  
  Buttons and controls include hover effects, transitions, and active states to enhance user interaction.
  
- **Visibility Classes**:  
  The `.hidden` class is used to toggle the visibility of elements dynamically (e.g., the price table and some P/L displays).

---

## 4. JavaScript Functionality

All the interactive behavior is driven by JavaScript, which runs when the DOM is fully loaded. The code is organized into several logical sections.

### 4.1 Global State and Initialization

- **Cached DOM Elements**:  
  The script starts by caching key elements (buttons, input fields, display panels) to minimize repeated DOM queries.

- **State Variables**:  
  Variables such as `action`, `lockedPrice`, `lockedQty`, `storedDiceValues`, `storedBid`, `storedOffer`, `cumulativePL`, and `simCycleDuration` maintain the current state of the simulation.

- **Chart Initialization**:  
  Using Chart.js, a line chart is set up to plot the cumulative P/L. Data is stored in `plData` and updated as trades occur.

### 4.2 Utility Functions

These helper functions are central to the game’s logic:

- **`logEvent(message)`**  
  Appends a new log entry with a timestamp to the log panel. This helps trace the simulation’s events.
  
- **`updatePLChart(pl)`**  
  Updates the cumulative P/L chart by adding the P/L from the current trade, updating both the data and labels in the chart.
  
- **`resetPurchaseState()`**  
  Resets variables related to the market participant’s purchase state, re-enabling buy/sell buttons and removing any temporary purchase information.
  
- **`updateDiceValues()`**  
  Randomizes dice outcomes. Each dice can show a number or an “H” (hidden). The function ensures at least one “H” is present (if not, it randomly replaces one value with “H”).
  
- **`updateBidOffer()`**  
  Randomly sets bid and offer prices for the Market Participant role. The bid is generated within a specified range, and the offer is set slightly higher.
  
- **`updateRealization()`**  
  Calculates the realized outcome of the dice roll. For Market Makers, it computes the net P/L based on stored market results; for Participants, it calculates P/L based on their locked price and action (buy or sell). It updates the display accordingly and triggers chart updates.
  
- **`animateProgressBar(duration)`**  
  Uses `requestAnimationFrame` to animate the progress bar over the set cycle duration. When the timer elapses, it triggers the realization update, resets game elements (dice, bid/offer), logs the cycle’s end, and restarts the cycle.
  
- **`setRole(role)`**  
  Toggles the UI between the two roles. Depending on the role selected:
  - For “maker”: the market maker controls are shown and participant-specific elements are hidden.
  - For “participant”: the participant controls and price table become visible, and the bid/offer are updated.

### 4.3 Event Listeners

Event listeners bind user interactions with their respective handlers:

- **Role Selection**:  
  Clicking on the Market Maker or Market Participant buttons sets the user’s role via `setRole(role)` and logs the event.
  
- **Market Participant Actions**:
  - **Buy Button**:  
    Locks in the action as “buy”, sets the locked price (using the offer price), and the quantity from user input. It disables further inputs until the cycle completes and logs the order.
  - **Sell Button**:  
    Similar to the buy button, but for a “sell” action using the bid price.
  
- **Market Maker Action**:
  - **Submit Market Order**:  
    Reads the midpoint and spread values from user input, calculates the bid and offer prices, and simulates market orders. It calculates the expected outcome using the stored dice values, derives buyer/seller estimates based on calculated edges, and logs the result.
  
- **Simulation Settings**:
  - **Cycle Duration Slider**:  
    Adjusts the simulation cycle duration (`simCycleDuration`) based on user input, updating the display and logging the new speed.
  - **Reset Simulation Button**:  
    Resets the cumulative P/L chart and game state (including dice values and bid/offer) and logs the reset event.

### 4.4 Simulation Cycle and Animation

- **Initialization Function `initializeGame()`**:  
  Sets the dice to a default state (all “H”) and updates the bid/offer.
  
- **Cycle Execution with `animateProgressBar(simCycleDuration)`**:  
  This function is called to kick off the simulation cycle. It:
  1. Animates the progress bar over the duration.
  2. On completion, calls `updateRealization()` to compute and display outcomes.
  3. Refreshes the dice values and bid/offer for the next cycle.
  4. Logs the cycle’s completion and recursively starts the next cycle.

---

## 5. Logic Flow Summary

1. **Initialization**:  
   - DOM is loaded; key elements are cached.
   - Global variables are set up, and the Chart.js line chart is initialized.
   - Default dice values and bid/offer prices are established.
   - The simulation cycle is started with an animated progress bar.

2. **Role Selection**:  
   - User chooses between Market Maker and Market Participant.
   - The UI adjusts accordingly, displaying either maker controls or participant controls with a price table.

3. **User Interaction**:
   - **Market Maker**:  
     - Sets market parameters (midpoint, spread).
     - Submits market orders, which calculate bid/offer and expected outcomes.
   - **Market Participant**:  
     - Chooses to buy or sell a specified quantity.
     - The order is “locked in” until the cycle ends.

4. **Cycle Execution**:
   - The progress bar animates over the defined cycle duration.
   - At the end of the cycle, dice values are updated and at least one “H” is enforced.
   - The realization value is computed by summing dice outcomes.
   - Depending on the role, the corresponding P/L is calculated and displayed.
   - The cumulative P/L chart is updated, and all events are logged.
   - The simulation automatically resets for a new cycle.

*Quick Gen Z note: It’s like a never-ending TikTok loop—always a fresh cycle, always new outcomes, always something to brag about in the chat!*

---

## 6. Conclusion

This documentation covers the entire structure and functionality of the Simulation Market-Making Game. The program seamlessly integrates HTML for layout, CSS for modern styling, and JavaScript for dynamic interaction and simulation. By combining real-time data visualization with interactive market operations, the game delivers an engaging and educational simulation of market-making. Whether you’re here to test your trading chops or just flex some market savvy, this simulation has got you covered.

*Stay ahead, stay savvy, and remember: in this simulation, every cycle is a new chance to make that virtual cheddar!*

---

*End of Documentation*
