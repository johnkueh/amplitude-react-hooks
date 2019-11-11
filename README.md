<div align="center">
<h1>Amplitude React Hooks</h1>

<a href="https://www.metricsjar.com/amplitude-react-hooks">
  <img
    height="60"
    width="60"
    alt="Logo"
    src="https://www.metricsjar.com/static/apple-touch-icon.png"
  />
</a>

React hooks implementation of [amplitude-js](https://github.com/amplitude/amplitude-javascript).

</div>

<hr />

## Installation

`yarn add amplitude-js amplitude-react-hooks`

Or if using npm

`npm install amplitude-js amplitude-react-hooks`

## How to use

First, wrap your app with the `AmplitudeProvider` component. Replace `API_KEY` with the Amplitude API Key given to you. You can find your project's API Key in your project's [Settings page](https://help.amplitude.com/hc/en-us/articles/235649848#project-general-settings).

```tsx
import React from "react";
import { AmplitudeProvider } from "amplitude-react-hooks";

const App = () => (
  <AmplitudeProvider API_KEY={`YOUR_AMPLITUDE_API_KEY`}>
    <MyRootComponent />
  </AmplitudeProvider>
);

render(<App />, document.getElementById("root"));
```

Under the hood, the library will load `amplitude-js` and call the following methods:

```js
amplitude.getInstance().init("YOUR_AMPLITUDE_API_KEY");
```

## Tracking pre-configured events

### User viewed a page

```tsx
import React from "react";
import { useAmplitude } from "amplitude-react-hooks";

const PricingPage = () => {
  const { trackPageViewed } = useAmplitude();

  // Fire once when component loads
  useEffect(() => {
    trackPageViewed("Pricing");
  }, []);

  return <div>Pricing page</div>;
};

export default PricingPage;
```

### User signed up

```tsx
import React from "react";
import { useAmplitude } from "amplitude-react-hooks";
import { performSignup } from "../../helpers/signup";

const SignupPage = () => {
  const { trackSignedUp } = useAmplitude();

  return (
    <form
      onSubmit={e => {
        e.preventDefault();
        const { user } = await performSignup();
        trackSignedUp({
          id: user.id,
          email: user.email,
          name: user.name
        });
      }}
    >
      <input type="email" placeholder="Email address" />
      <input type="name" placeholder="Name" />
      <input type="password" placeholder="Password" />
      <button type="submit">Sign up</button>
    </form>
  );
};

export default SignupPage;
```

Under the hood, the library will call the following methods:

```js
amplitude.getInstance().logEvent("Signed up", {
  id: "123",
  email: "email@example.com",
  name: "John Doe"
});

var userProperties = {
  id: "123",
  email: "email@example.com",
  name: "John Doe"
};
amplitude.getInstance().setUserProperties(userProperties);
```

### User upgraded to a paid plan

```tsx
import React from "react";
import { useAmplitude } from "amplitude-react-hooks";
import { subscribeToPlan } from "../../helpers/billing";

const PaymentsPage = () => {
  const { trackUpgradedToPaid } = useAmplitude();

  return (
    <form
      onSubmit={e => {
        e.preventDefault();
        const { subscription } = await subscribeToPlan({
          planId: "basic",
          interval: "month",
          source: 'tok_mastercard,
          customerId: 'cus_9fYy2VJUHCLMB1'
        });
        trackUpgradedToPaid({
          id: subscription.id,
          plan: subscription.planId,
          interval: subscription.interval,
          amount: subscription.amount,
        });
      }}
    >
      <input name="cc_number" placeholder="Credit card number" />
      <input name="exp_month" placeholder="Expiry month" />
      <input name="exp_year" placeholder="Expiry year" />
      <input name="last4" placeholder="Security Code" />
      <input name="interval" value="month" type="radio" checked />
      <input name="interval" value="year" type="radio" />
      <select name="planId" value="basic">
        <option value="basic">Basic</option>
        <option value="premium">Premium</option>
        <option value="enterprise">Enterprise</option>
      </select>
      <button type="submit">Subscribe</button>
    </form>
  );
};

export default PaymentsPage;
```

Under the hood, the library will call the following methods:

```js
amplitude.getInstance().logEvent("Upgraded To Paid Plan", {
  id: "sub_9h6CopvY0Fldnj",
  planId: 'basic';
  billingInterval: 'month',
  amount: 99000,
  paymentProviderCustId: 'cus_9fYy2VJUHCLMB1';
});

var userProperties = {
  id: "123",
  plan: {
    id: 'basic',
    interval: 'month'
    amount: 99000,
    subscriptionId: 'sub_9h6CopvY0Fldnj',
    customerId: 'cus_9fYy2VJUHCLMB1',
  }
};
amplitude.getInstance().setUserProperties(userProperties);
```

## Tracking custom events

### Track any event

```tsx
import React from "react";
import { useAmplitude } from "amplitude-react-hooks";
import { performSignup } from "../../helpers/signup";

const SignupPage = () => {
  const { track } = useAmplitude();

  return (
    <form
      onSubmit={e => {
        e.preventDefault();
        const { user } = await performSignup();
        track("Signed Up", {
          id: user.id,
          email: user.email,
          name: user.name
        });
      }}
    >
      <input type="email" placeholder="Email address" />
      <input type="name" placeholder="Name" />
      <input type="password" placeholder="Password" />
      <button
        onClick={e => {
          track("Clicked Sign Up");
        }}
        type="submit"
      >
        Sign up
      </button>
    </form>
  );
};

export default SignupPage;
```

Under the hood, the library will call the following methods:

```js
amplitude.getInstance().logEvent("Signed up", {
  id: "123",
  email: "email@example.com",
  name: "John Doe"
});
```

## Identifying users

### Setting user properties to identify the user

```tsx
import React from "react";
import { useAmplitude } from "amplitude-react-hooks";
import { performSignup } from "../../helpers/signup";

const SignupPage = () => {
  const { identify } = useAmplitude();

  return (
    <form
      onSubmit={e => {
        e.preventDefault();
        const { user } = await performSignup();
        identify({
          id: user.id,
          email: user.email,
          name: user.name
        });
      }}
    >
      <input type="email" placeholder="Email address" />
      <input type="name" placeholder="Name" />
      <input type="password" placeholder="Password" />
      <button type="submit">Sign up</button>
    </form>
  );
};

export default SignupPage;
```

Under the hood, the library will call the following methods:

```js
var userProperties = {
  id: "123",
  email: "email@example.com",
  name: "John Doe"
};
amplitude.getInstance().setUserProperties(userProperties);
```
