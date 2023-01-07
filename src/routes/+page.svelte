<script lang="ts">
	import { writable, type Writable, type Readable, type Unsubscriber } from 'svelte/store';

	import { Observable, from } from 'rxjs';

	function sveltePipe<T>(svelteStore: Writable<T> | Readable<T>): Observable<T> {
		let unsub: Unsubscriber;
		const obs = new Observable<T>((subscriber) => {
			unsub = svelteStore.subscribe((val) => {
				console.log('Next on value');
				subscriber.next(val);
			});

			return () => {
				console.log('Unsubscribing the svelte store');
				unsub();
			};
		});

		return obs;
	}

	import { map } from 'rxjs/operators';
	const w = writable<number>(0);
	const stuff = sveltePipe<number>(w).pipe(map((x) => x + 10));
	setTimeout(() => {
		w.set(2);
	}, 3000);
</script>

<h1>The count is {$stuff} or {$w}</h1>
<a href="/other">Go to other page so we can see unsubscribe run</a>
