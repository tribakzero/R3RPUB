# BR360: Bookmarklet for Risco 360
`ㅤ/ˈbre.ɡo/ ㅤ`

There is a common issue with the insurance provider and their minor medical expenses service, they don't let you know how much you have expended per user, making it hard to keep track of it and know how much you still can reimburse.

That's why I reverse engineered it and created this script that gets the data the same way they do and serves it back to you in the way you want it: split by user instead of as a single big number.

## Usage

Just drag the bookmarklet to your browser's bookmarks bar: <a href="javascript:%22use%20strict%22;void%20function(){const%20a=ontdc(80,obtenerValorParametro(%22tkn%22),document.getElementById(%22usrSs%22).innerText),b=a.reduce((a,b)=%3E1===b.Estatus%3Fa:(a.hasOwnProperty(b.NameAfectado)%3Fa[b.NameAfectado]+=b.TTReembolso:a[b.NameAfectado]=b.TTReembolso,a),{}),c=Object.entries(b).map(a=%3E{let[b,c]=a;return`${b}:%20$${c.toFixed(2)}`}).join(%22\n%22);alert(c)}();">BR36O</a>

Then go to your Risco 360 website and click BR360 bookmarklet once to see a new window pop-up with the totals grouped by user.

## How it works

If you want to know what does the script do exactly, here is a less cryptic version of it.

```javascript
// This function retrieves Risco 360 data
const records = ontdc(
  // Internal Code for reimbursement requests
  80,
  // Your auth token
  obtenerValorParametro('tkn'),
  // Your user ID
  document.getElementById("usrSs").innerText
);

// Groups the reimbursement amounts by user
const grouped = records.reduce((totals, record) => {
  // We only care about already paid requests
  if(record.Estatus === 1) return totals;

// Checks if the name already exists
  if(totals.hasOwnProperty(record.NameAfectado)) {
    // So it adds the current value to the accumulated
    totals[record.NameAfectado] += record.TTReembolso
  }
  else {
    // Otherwise, it creates the new record
    totals[record.NameAfectado ] = record.TTReembolso;
  }

  // And saves it
  return totals;
},{});

// Writes the data with the format: "NAME: $AMOUNT"
const prettyPrint = Object.entries(grouped).map(
  ([name, amount]) => `${name}: $${amount.toFixed(2)}`
).join('\n');

alert(prettyPrint); // Prints the values out
```

In general terms, it uses the same function they use internally to get your records, then puts it into a hashmap to get the totals by each user and then sends a human readable version of it to you.

## Credits

It all got minified using: https://chriszarate.github.io/bookmarkleter/
