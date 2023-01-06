<script lang="ts">
	import { writable, type Writable, type Readable, type Unsubscriber } from 'svelte/store';

	import { Observable } from 'rxjs';

	function sveltePipe<T>(w: Writable<T> | Readable<T>): Observable<T> {
		let unsub: Unsubscriber;
		const obs = new Observable<T>((subscriber) => {
			unsub = w.subscribe((val) => {
				subscriber.next(val);
			});
		});

		// how to unsubscribe the inner subscription?
		const unsubscribe = (x: any) => {
			unsub();
		};

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
