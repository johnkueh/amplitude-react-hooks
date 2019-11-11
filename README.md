# amplitude-react-hooks

Use [amplitude-js](https://github.com/amplitude/amplitude-javascript) as React hooks.

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

### Tracking events

Page views

```tsx
import React from "react";
import { useAmplitude } from "amplitude-react-hooks";

const PricingPage = () => {
  const { trackPageView } = useAmplitude();

  // Fire once when component loads
  useEffect(() => {
    trackPageViewed("Pricing");
  }, []);

  return <div>Pricing page</div>;
};

export default PricingPage;
```

Signed up

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

Under the hood, the library will call the following methods

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

### Setting user properties
