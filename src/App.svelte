<script>
  import { initializeApp } from "firebase/app";
  import {
    getAuth,
    GoogleAuthProvider,
    signInWithCredential,
    signOut as signOutFB,
    onAuthStateChanged
  } from "firebase/auth";

  import { FirebaseAuthentication } from "@robingenz/capacitor-firebase-authentication";
import { writable } from "svelte/store";
import { onMount } from "svelte";

  const firebaseConfig = {
    // ... firebase config here \\
  };
  const app = initializeApp(firebaseConfig);
  const auth = getAuth();

  const getCurrentUser = async () => {
    const result = await FirebaseAuthentication.getCurrentUser();
    return result.user;
  };

  const signInWithGoogle = async () => {
    const res = await FirebaseAuthentication.signInWithGoogle();
    const cred = GoogleAuthProvider.credential(res.credential?.idToken);
    await signInWithCredential(auth, cred);
  };

  const signOut = async () => {
    await FirebaseAuthentication.signOut();
    await signOutFB(auth);
  };

  const user = writable(null)

  onMount(() => {
    const t = getCurrentUser()
    if (t) $user = t
    const unsub = onAuthStateChanged(auth, (currentUser) => {
      $user = currentUser
      
    })

    return () => unsub()
  })
</script>


<main>

  <h2>Welcome</h2>

  {#if $user}
    <h1>{$user.displayName}</h1>
  {:else}
    <h1>Not logged in!</h1>
  {/if}


  <button on:click={signInWithGoogle}>Login with Google</button>
  <button on:click={signOut}>Logout</button>

</main>
