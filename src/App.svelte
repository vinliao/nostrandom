<script>
  import * as secp from "@noble/secp256k1";

  // hhahhaah bots will scrape this and think they found some stash
  const privateKey = "13ea3d37b40b103db640b7bd84ba6d5664aee32d22d2b08868853e98f1286430";

  // dummy event, for reference
  let dummyEvent = {
    id: "052802817e1ffa0d79716b79d842da545cc8abe0529e0896c3a634b9ba2ba77c",
    pubkey: "62bf2ecb0dd7ee7ba7f79b551927573203ee0061491ada5ec02414e265ba2d42",
    created_at: 1643011993,
    kind: 1,
    tags: [],
    content: "dummy data",
    sig:
      "51728a6f3dc49e78b578086507fe7c18c48a3936830d0ae0089918198641de09a31af95e3d0a731863d90bcd5f774478d2e9b0374bb4e120d5e19fddacf89376",
  };
  let event;

  function toHexString(byteArray) {
    return Array.prototype.map
      .call(byteArray, function (byte) {
        return ("0" + (byte & 0xff).toString(16)).slice(-2);
      })
      .join("");
  }

  async function createEvent() {
    let event = {};
    event.pubkey = dummyEvent.pubkey;
    event.created_at = Date.now();
    event.kind = 1;
    event.tags = [];
    event.content = "From Nostrandom";

    // id is sha256 of data above
    // sig is schnorr sig of id
    const eventString = JSON.stringify(event);
    const eventByteArray = new TextEncoder().encode(eventString);
    const eventIdRaw = await secp.utils.sha256(eventByteArray);
    const eventId = toHexString(eventIdRaw);

    const signatureRaw = await secp.schnorr.sign(eventId, privateKey);
    const signature = toHexString(signatureRaw);

    event.id = eventId;
    event.sig = signature;

    return event;
  }

  (async () => {
    event = await createEvent();
  })();

</script>

<main>
  <p>{JSON.stringify(event)}</p>
</main>
