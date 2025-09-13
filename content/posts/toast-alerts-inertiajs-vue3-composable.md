+++
title = 'Mastering Toast Alerts in Inertia.js with Vue 3: From Basics to Reusability'
description = 'Mastering Toast Alerts in Inertia.js with Vue 3: From Basics to Reusability'
date = 2025-09-13T17:21:27+05:45
draft = false
+++

In modern web development, providing seamless user feedback is crucial for a great user experience. Inertia.js bridges the gap between Laravel (or other backends) and frontend frameworks like Vue 3, allowing you to build single-page applications without the complexity of traditional SPAs. One common feature is displaying toast notifications those subtle, non-intrusive alerts that pop up to inform users of success, errors, or other events.

In this blog post, we'll explore different ways to implement toast alerts using Inertia.js and Vue 3, with examples leveraging the popular `vue3-toastify` library for toasts. We'll start with the basics and progress to more advanced techniques, including handling authorization errors and extracting logic into reusable composables. By the end, you'll have a step-by-step guide to making your alerts efficient and maintainable.

#### 1. The Basic Approach: Using onSuccess Callbacks
The simplest way to trigger a toast alert is directly in response to form submissions or API calls via Inertia's `useForm` helper. Inertia allows you to handle server responses with callbacks like `onSuccess`, where you can check for flash messages passed from the backend.
Flash messages in Laravel are temporary session data (e.g., `return redirect()->route('venues.index')->with('success', 'Action completed!')`) that Inertia makes available in Vue via `page.props.flash`.
Here's an example in a Vue component for submitting a form:

```vue
<script setup>
import { useForm } from '@inertiajs/vue3';
import { toast } from 'vue3-toastify';

const form = useForm({
    //
});

const submit = () => {
    form.post('/your-route', {
        onSuccess: (page) => {
            if (page.props.flash?.success) {
                toast.success(page.props.flash.success);
            }
            if (page.props.flash?.error) {
                toast.error(page.props.flash.error);
            }
        },
    });
};
</script>
```

In this setup:
- On successful form submission, the backend flashes a message.
- The onSuccess callback accesses the updated page props and triggers the appropriate toast.
- This is ideal for actions like creating or updating records, where feedback is tied to a specific user interaction.

Pros: Straightforward and event-specific.

Cons: Limited to form submissions; doesn't handle page-load scenarios automatically.

#### 2. Handling Authorization Errors Gracefully

Out-of-the-box, Laravel's authorization (via Gates or Policies) might throw a 403 error, leading to a generic "Unauthorized" page. Instead, you can customize this to redirect users with a flash message, which can then be displayed as a toast.

On the Laravel side, in your controller, use `Gate::allows` to check permissions and flash an error if denied:

```php
use Illuminate\Support\Facades\Gate;

// In your controller method, e.g., update()
if (!Gate::allows('update', $venue)) {
    return redirect()->route('venues.index')
        ->with('error', 'You are not authorized to update this venue.');
}
```

#### 3. Displaying Flash Messages on Page Load

Authorization checks like `Gate::allows` in controllers often happen before rendering the page, so flash messages might already be present in `page.props` on initial load. However, they won't automatically show as toasts, you need to handle this in your Vue component.

Use Vue's `onMounted` lifecycle hook to check for flash messages when the component mounts:

```vue
<script setup>
import { usePage } from '@inertiajs/vue3';
import { onMounted } from 'vue';
import { toast } from 'vue3-toastify';

const page = usePage();

onMounted(() => {
    if (page.props.flash?.success) {
        toast.success(page.props.flash.success);
    }
    if (page.props.flash?.error) {
        toast.error(page.props.flash.error);
    }
});
</script>
```

This is perfect for scenarios like:

- Redirects after authorization failures (as in Section 2).
- Flash messages set during server-side rendering or redirects.

Place this in components like your edit page (e.g., `Edit.vue`) to ensure alerts appear immediately on load.

Pros: Handles non-interactive scenarios like redirects.

Cons: If you have many pages, you'll end up duplicating this code, violating DRY (Don't Repeat Yourself) principles.

#### 4. Extracting Logic into a Reusable Composable

To avoid code duplication across multiple pages, extract the flash message handling into a Vue composable. Composables are functions that encapsulate and reuse stateful logic in Vue 3.

Create a new file, e.g., `composables/useFlashMessages.js:`

```js
import { usePage } from '@inertiajs/vue3';
import { onMounted } from 'vue';
import { toast } from 'vue3-toastify';

export function useFlashMessages() {
    const page = usePage();

    onMounted(() => {
        if (page.props.flash?.success) {
            toast.success(page.props.flash.success);
        }
        if (page.props.flash?.error) {
            toast.error(page.props.flash.error);
        }
    });
}
```

Now, import and use it in any component:

```vue
<script setup>
import { useFlashMessages } from '@/composables/useFlashMessages';

useFlashMessages();
</script>
```

This makes your codebase cleaner, easier to maintain, and scalable for larger apps.

#### Conclusion

Toast alerts in Inertia.js with Vue 3 can range from simple callback-based implementations to sophisticated, reusable composables. Start with basics for quick wins, handle edge cases like authorization with flash redirects, and always refactor for reusability to keep your code DRY.

If you have questions or variations, drop a comment below! Happy coding! 🚀
