<script>
  import * as secp from "@noble/secp256k1";

  const privateKey = secp.utils.randomPrivateKey();
  const publicKey = secp.schnorr.getPublicKey(privateKey);
  
  // dummy event, for reference
  // const dummyEvent = {
  //   id: "052802817e1ffa0d79716b79d842da545cc8abe0529e0896c3a634b9ba2ba77c",
  //   pubkey: "62bf2ecb0dd7ee7ba7f79b551927573203ee0061491ada5ec02414e265ba2d42",
  //   created_at: 1643011993,
  //   kind: 1,
  //   tags: [],
  //   content: "dummy data",
  //   sig:
  //     "51728a6f3dc49e78b578086507fe7c18c48a3936830d0ae0089918198641de09a31af95e3d0a731863d90bcd5f774478d2e9b0374bb4e120d5e19fddacf89376",
  // };
  let event;

  function toHexString(byteArray) {
    return Array.prototype.map
      .call(byteArray, function (byte) {
        return ("0" + (byte & 0xff).toString(16)).slice(-2);
      })
      .join("");
  }

  async function createEvent() {
    const content = "From Nostrandom https://nostrandom.netlify.app";
    const unixTime = Math.floor(Date.now() / 1000);
    const data = [0, toHexString(publicKey), unixTime, 1, [], content];

    // id is sha256 of data above
    // sig is schnorr sig of id
    const eventString = JSON.stringify(data);
    const eventByteArray = new TextEncoder().encode(eventString);
    const eventIdRaw = await secp.utils.sha256(eventByteArray);
    const eventId = toHexString(eventIdRaw);

    const signatureRaw = await secp.schnorr.sign(eventId, privateKey);
    const signature = toHexString(signatureRaw);

    return {
      id: eventId,
      pubkey: toHexString(publicKey),
      created_at: unixTime, 
      kind: 1,
      tags: [],
      content: content, 
      sig: signature
    }
  }

  (async () => {
    event = await createEvent();
  })();

</script>

<main>
  <pre>{JSON.stringify(event, null, 2)}</pre>

  <p>The event above is a publish-able event you can manually publish to Nostr relays.</p>
  <p>This site doesn't publish event, it only generates event.</p>
  <p>Refresh page to generate new event.</p>
  <p>For PRs and issues: <a href="https://github.com/vinliao/nostrandom">github.com/vinliao/nostrandom</a>.</p>
</main>
