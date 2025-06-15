# **Joseph Gabito**  
![Header](https://github.com/user-attachments/assets/d26e0c0e-04d7-4dbb-8bb5-8195729b8692)

#### <small> I've seen WordPress plugins with better architecture than this... said no one ever." (drops mic made of recycled global $wpdb objects) 🎤 </small>

> **"Your `functions.php` file called. It said: 'Kill me.'"**

👋 **Hey there, I'm Joseph**  

I'm a software engineer with **16+ years of formal experience** (20+ if you include all those self-inflicted all-nighters) turning WordPress plugins from spaghetti nightmares into clean, testable masterpieces. I specialize in **Domain-Driven Design**, **Clean Architecture**, and **Event Sourcing**, basically, giving legacy code the therapy it desperately needs.

---

## **🍽️ Case Studies: From Dumpster Fire to Michelin Star**

### **🌟🌟🌟 Case Study #1: Multi-Provider AI Framework**
**The Challenge:** *"We need OpenAI, Claude, Gemini, Cohere, Hugging Face, Replicate, Together AI, and Perplexity. Timeline? Three days."*

**BEFORE: "Pray to the Demo Gods" Architecture**
```php
// This is why developers have trust issues
function get_ai_response($provider, $prompt) {
    if ($provider === 'openai') {
        $response = wp_remote_post('https://api.openai.com/v1/chat/completions', [
            'headers' => ['Authorization' => 'Bearer ' . get_option('openai_key')],
            'body' => json_encode(['model' => 'gpt-4', 'messages' => [['role' => 'user', 'content' => $prompt]]])
        ]);
        return json_decode(wp_remote_retrieve_body($response))['choices'][0]['message']['content'];
        
    } elseif ($provider === 'claude') {
        // *cries in copy-paste* - Another 50 lines of horror
        $response = wp_remote_post('https://api.anthropic.com/v1/messages', [
            'headers' => ['x-api-key' => get_option('claude_key'), 'anthropic-version' => '2023-06-01'],
            'body' => json_encode(['model' => 'claude-3-sonnet-20240229', 'max_tokens' => 1000, 'messages' => [['role' => 'user', 'content' => $prompt]]])
        ]);
        return json_decode(wp_remote_retrieve_body($response))['content'][0]['text'];
    }
    // ... 47 more if/else statements because self-hatred
}
```

**AFTER: Michelin Star AI Architecture ⭐⭐⭐**
```php
// Interface-driven perfection
interface AI_Provider_Interface {
    public function create_builder(): Payload;
    public function send_request(Request $payload): array;
    public function parse_response(array $response): Response;
}

// Provider Factory Magic - 8 providers in 8 lines
Provider_Factory::register_provider('OPENAI', OpenAI_Provider::class);
Provider_Factory::register_provider('CLAUDE', Claude_Provider::class);
Provider_Factory::register_provider('DEEPSEEK', Deepseek_Provider::class);
Provider_Factory::register_provider('PERPLEXITY', Perplexity_Provider::class);
Provider_Factory::register_provider('GEMINI', Gemini_Provider::class);
Provider_Factory::register_provider('COHERE', Cohere_Provider::class);
Provider_Factory::register_provider('GROK', Grok_Provider::class);
Provider_Factory::register_provider('MISTRAL', Mistral_Provider::class);

// Fluent API that reads like poetry
$response = $provider->create_builder()
    ->endpoint('https://api.anthropic.com/v1/messages')
    ->authorization($api_key)
    ->json_content()
    ->model('claude-3-sonnet-20240229')
    ->temperature(0.7)
    ->max_completion_tokens(2048)
    ->messages($conversation_history)
    ->build();

// Immutable value objects for bulletproof responses
final class Response {
    public function get_content(): string { /* Works for ALL providers */ }
    public function get_meta_data(): array { /* Standardized metadata */ }
    public function get_raw(): array { /* Full debug access */ }
}
```

**The Michelin Star Results:**
- ⭐ **Added 8 providers in 3 days** (vs. 3 months for one)
- ⭐ **25 lines per provider** (vs. 150+ lines of copy-paste)
- ⭐ **Zero bugs in production** (vs. weekly hotfixes)
- ⭐ **100% test coverage** (vs. "it works on my machine")

---

### **🏪 Case Study #2: E-Commerce Integration Rescue**
**The Challenge:** *"Our WooCommerce integration is held together with duct tape and crashes every Black Friday."*

**BEFORE: "It Works Until It Doesn't"**
```php
// This code has PTSD
function handle_woocommerce_order() {
    global $wpdb, $woocommerce;
    
    if (isset($_POST['order_id'])) {
        $order_id = $_POST['order_id'];
        
        // Business logic mixed with database queries
        $items = $wpdb->get_results("SELECT * FROM {$wpdb->prefix}woocommerce_order_items WHERE order_id = $order_id");
        
        foreach ($items as $item) {
            // Inventory management in a foreach loop because YOLO
            $product_id = $wpdb->get_var("SELECT meta_value FROM {$wpdb->prefix}woocommerce_order_itemmeta 
                                        WHERE order_item_id = {$item->order_item_id}");
            
            $stock = get_post_meta($product_id, '_stock', true);
            update_post_meta($product_id, '_stock', $stock - $item->quantity);
            
            // Third-party APIs without error handling
            wp_remote_post('https://api.fulfillment.com/ship', [
                'body' => json_encode(['product_id' => $product_id])
            ]);
        }
    }
}
```

**AFTER: Domain-Driven Design Excellence**
```php
// Clean architecture that survives Black Friday
class Order_Completion_Handler {
    public function __construct(
        private Inventory_Service $inventory,
        private Fulfillment_Service $fulfillment,
        private Notification_Service $notifications,
        private Order_Repository $orders
    ) {}
    
    public function handle(Order_Completed_Event $event): void {
        $order = $this->orders->find($event->order_id);
        
        try {
            foreach ($order->get_items() as $item) {
                $inventory_result = $this->inventory->reserve_stock($item);
                
                if ($inventory_result->is_low_stock()) {
                    $this->notifications->send_low_stock_alert($item->product);
                }
                
                $this->fulfillment->schedule_shipment($item, $order->shipping_address);
            }
            
            // Event sourcing for complete audit trail
            $this->orders->record_event(
                new Order_Processing_Completed_Event($order->id, $order->items)
            );
            
        } catch (Inventory_Exception | Fulfillment_Exception $e) {
            $this->orders->record_event(
                new Order_Processing_Failed_Event($order->id, $e->getMessage())
            );
            throw $e;
        }
    }
}
```

**Results:** ✅ Survived 3 Black Fridays ✅ Zero downtime ✅ 50% faster processing

---

### **🏗️ Case Study #3: Subscription Management System**
**The Challenge:** *"Our subscription logic is scattered across 15 files and breaks every time we add a payment method."*

**BEFORE: "WordPress Spaghetti Subscription Hell"**
```php
// Scattered procedural nightmare across multiple files
function handle_subscription_signup() {
    global $wpdb;
    
    if (isset($_POST['plan_id'])) {
        $plan_id = sanitize_text_field($_POST['plan_id']);
        $user_id = get_current_user_id();
        
        // Business logic mixed with database operations
        $plan = $wpdb->get_row("SELECT * FROM {$wpdb->prefix}subscription_plans WHERE id = $plan_id");
        
        if ($plan->price > 0) {
            // Payment processing scattered in different functions
            if ($_POST['payment_method'] === 'stripe') {
                include_once 'stripe-handler.php';
                $charge = stripe_charge_customer($_POST['card_token'], $plan->price);
                
                if ($charge->status === 'succeeded') {
                    // Subscription creation mixed with payment logic
                    $wpdb->insert($wpdb->prefix . 'subscriptions', [
                        'user_id' => $user_id,
                        'plan_id' => $plan_id,
                        'status' => 'active',
                        'next_billing' => date('Y-m-d', strtotime('+1 month'))
                    ]);
                    
                    // Email notifications buried in payment flow
                    wp_mail(get_user_by('id', $user_id)->user_email, 'Welcome!', 'You are subscribed!');
                }
            } elseif ($_POST['payment_method'] === 'paypal') {
                // Copy-paste PayPal logic with slight variations
                include_once 'paypal-handler.php';
                // ... another 50 lines of duplicate logic
            }
        }
    }
}

// Renewal logic in a different file with global dependencies
function process_subscription_renewals() {
    global $wpdb;
    
    $due_subscriptions = $wpdb->get_results(
        "SELECT * FROM {$wpdb->prefix}subscriptions WHERE next_billing <= NOW() AND status = 'active'"
    );
    
    foreach ($due_subscriptions as $subscription) {
        // Payment method logic duplicated again
        $user_meta = get_user_meta($subscription->user_id, 'payment_method', true);
        
        if ($user_meta === 'stripe') {
            // Another copy of Stripe logic
        }
    }
}
```

**AFTER: Domain-Driven Design That Actually Works**
```php
// Clean domain separation with proper boundaries
namespace SubscriptionManagement\Domain {
    
    // Value objects for type safety
    final class Subscription_ID {
        public function __construct(private string $id) {}
        public function value(): string { return $this->id; }
    }
    
    final class Money {
        public function __construct(
            private float $amount,
            private Currency $currency
        ) {}
        
        public function is_free(): bool { return $this->amount === 0.0; }
    }
    
    // Domain entity with business rules
    class Subscription {
        public function __construct(
            private Subscription_ID $id,
            private User_ID $user_id,
            private Plan $plan,
            private Subscription_Status $status,
            private Payment_Method $payment_method,
            private \DateTimeImmutable $next_billing_date
        ) {}
        
        public function renew(): Subscription_Renewal_Result {
            if (!$this->is_active()) {
                return Subscription_Renewal_Result::failed('Subscription not active');
            }
            
            if (!$this->is_due_for_renewal()) {
                return Subscription_Renewal_Result::failed('Not due for renewal');
            }
            
            return Subscription_Renewal_Result::ready_for_payment();
        }
        
        public function cancel(): void {
            $this->status = Subscription_Status::cancelled();
            $this->record_event(new Subscription_Cancelled_Event($this->id));
        }
        
        private function is_due_for_renewal(): bool {
            return $this->next_billing_date <= new \DateTimeImmutable();
        }
    }
}

namespace Payment\Domain {
    // Payment processing completely separate from subscriptions
    interface Payment_Gateway_Interface {
        public function charge(Payment_Request $request): Payment_Result;
        public function create_recurring_payment(Recurring_Payment_Request $request): Payment_Result;
    }
    
    class Stripe_Gateway implements Payment_Gateway_Interface {
        public function charge(Payment_Request $request): Payment_Result {
            // Clean, focused payment logic
        }
    }
    
    class PayPal_Gateway implements Payment_Gateway_Interface {
        public function charge(Payment_Request $request): Payment_Result {
            // Same interface, different implementation
        }
    }
}

// Application service coordinates domains
namespace SubscriptionManagement\Application {
    class Subscription_Creation_Service {
        public function __construct(
            private Subscription_Repository $subscriptions,
            private Payment_Service $payments,
            private Email_Service $emails,
            private Event_Store $events
        ) {}
        
        public function create_subscription(Create_Subscription_Command $command): Subscription_Creation_Result {
            $plan = $this->plans->find($command->plan_id);
            
            // Domain logic: handle payment only if plan has cost
            if (!$plan->is_free()) {
                $payment_result = $this->payments->process_payment(
                    $command->payment_method,
                    $plan->price,
                    $command->user_id
                );
                
                if ($payment_result->failed()) {
                    return Subscription_Creation_Result::payment_failed($payment_result->error);
                }
            }
            
            // Create subscription after successful payment
            $subscription = Subscription::create($command->user_id, $plan, $command->payment_method);
            $this->subscriptions->save($subscription);
            
            // Send welcome email
            $this->emails->send_subscription_welcome($subscription);
            
            // Record domain event for analytics/integrations
            $this->events->record(new Subscription_Created_Event($subscription));
            
            return Subscription_Creation_Result::success($subscription);
        }
    }
}
```

**Why This Architecture Wins:**
✅ **Single Responsibility** - Each class has one job  
✅ **Payment Gateway Abstraction** - Add new payment methods easily  
✅ **Domain Events** - Clean audit trail and integrations  
✅ **Type Safety** - Value objects prevent invalid states  
✅ **Testable** - Mock repositories and services  

**Results:** ✅ **Added 4 payment gateways in 2 days** ✅ **Zero payment bugs** ✅ **Clean subscription logic**

---

### **💀 Case Study #4: Filter Abuse Recovery Program**
**The Challenge:** *"Our codebase has 847 filters and debugging feels like archaeological excavation."*

**BEFORE: "Filter Hell - Every Function Call is a Hook"**
```php
// The nightmare of unnecessary filter indirection
class Subscription_Service {
    public function create_subscription($user_id, $plan_id) {
        // Why call a function when you can add unnecessary complexity?
        $user = apply_filters('subscription_get_user', null, $user_id);
        $plan = apply_filters('subscription_get_plan', null, $plan_id);
        
        // Business logic drowned in hooks
        $validation_result = apply_filters('subscription_validate_user', true, $user, $plan);
        if (!$validation_result) {
            return apply_filters('subscription_validation_failed', false, $user, $plan);
        }
        
        // Even simple calculations become filter chains
        $price = apply_filters('subscription_calculate_price', $plan->price, $user, $plan);
        $tax = apply_filters('subscription_calculate_tax', 0, $price, $user);
        $total = apply_filters('subscription_calculate_total', $price + $tax, $price, $tax);
        
        // Core business logic lost in filter soup
        do_action('subscription_before_payment', $user, $plan, $total);
        $payment_result = apply_filters('subscription_process_payment', null, $user, $total);
        do_action('subscription_after_payment', $payment_result);
        
        if (apply_filters('subscription_payment_successful', $payment_result->success, $payment_result)) {
            do_action('subscription_before_create', $user, $plan);
            $subscription = apply_filters('subscription_create', null, $user, $plan);
            do_action('subscription_after_create', $subscription);
            
            return apply_filters('subscription_creation_result', $subscription, $user, $plan);
        }
        
        return apply_filters('subscription_payment_failed', false, $payment_result);
    }
}

// Debugging nightmare: trace through 20+ filter calls to find the bug
function debug_subscription_issue() {
    // Step 1: Check which filter modified the user
    // Step 2: Check which filter modified the plan  
    // Step 3: Check which filter modified the validation
    // Step 4: Check which filter modified the price
    // Step 5: Give up and cry
}
```

**AFTER: Clean Architecture with Strategic WordPress Adapter**
```php
// Pure business logic - no WordPress coupling
namespace SubscriptionManagement\Domain {
    class Subscription_Creation_Service {
        public function __construct(
            private User_Repository $users,
            private Plan_Repository $plans,
            private Payment_Service $payments,
            private Subscription_Repository $subscriptions
        ) {}
        
        public function create_subscription(Create_Subscription_Command $command): Subscription_Result {
            // Pure business logic - direct function calls
            $user = $this->users->find($command->user_id);
            $plan = $this->plans->find($command->plan_id);
            
            // Domain validation
            if (!$this->can_user_subscribe($user, $plan)) {
                return Subscription_Result::validation_failed('User cannot subscribe to this plan');
            }
            
            // Price calculation in domain service
            $pricing = $this->pricing_service->calculate_subscription_cost($user, $plan);
            
            // Process payment
            $payment_result = $this->payments->process_payment($pricing->total, $user->payment_method);
            
            if ($payment_result->failed()) {
                return Subscription_Result::payment_failed($payment_result->error);
            }
            
            // Create subscription
            $subscription = Subscription::create($user, $plan, $payment_result);
            $this->subscriptions->save($subscription);
            
            return Subscription_Result::success($subscription);
        }
    }
}

// WordPress Adapter Layer - Strategic Filter Placement
namespace SubscriptionManagement\Infrastructure\WordPress {
    class WP_Subscription_Adapter {
        public function __construct(private Subscription_Creation_Service $service) {}
        
        public function handle_subscription_request(): void {
            // WordPress layer - THIS is where filters belong
            $command = $this->build_command_from_request();
            
            // Strategic filter: Allow modification of command before processing
            $command = apply_filters('automator_subscription_command', $command);
            
            $result = $this->service->create_subscription($command);
            
            if ($result->successful()) {
                // Strategic filter: Allow third-parties to react to success
                do_action('automator_subscription_created', $result->subscription, $command);
                
                // Strategic filter: Allow modification of success response
                $response = apply_filters('automator_subscription_success_response', [
                    'success' => true,
                    'subscription_id' => $result->subscription->id
                ], $result);
                
            } else {
                // Strategic filter: Allow third-parties to handle failures
                do_action('automator_subscription_failed', $result->error, $command);
                
                $response = apply_filters('automator_subscription_error_response', [
                    'success' => false,
                    'error' => $result->error
                ], $result);
            }
            
            wp_send_json($response);
        }
        
        private function build_command_from_request(): Create_Subscription_Command {
            // Convert WordPress request to domain command
            return new Create_Subscription_Command(
                user_id: get_current_user_id(),
                plan_id: sanitize_text_field($_POST['plan_id']),
                payment_method: sanitize_text_field($_POST['payment_method'])
            );
        }
    }
}

// Clean integration with WordPress
class Subscription_Integration {
    public function __construct(private WP_Subscription_Adapter $adapter) {}
    
    public function register_hooks(): void {
        // Only ONE WordPress hook needed
        add_action('wp_ajax_create_subscription', [$this->adapter, 'handle_subscription_request']);
    }
}
```

**The Strategic Filter Philosophy:**
✅ **3 thoughtful filters** vs 847 random ones  
✅ **WordPress layer only** - business logic stays pure  
✅ **Clear extension points** - third-parties know where to hook  
✅ **Maintainable** - each filter has a specific purpose  
✅ **Debuggable** - direct function calls in business logic  

**Why Every Filter is a Liability:**
- 🔍 **Debugging complexity** - trace through filter chains
- 🐛 **Hidden dependencies** - filters can break other filters  
- ⚡ **Performance overhead** - unnecessary indirection
- 📚 **Cognitive load** - developers must understand entire filter ecosystem
- 🔧 **Maintenance burden** - every filter must be documented and tested

**Results:** ✅ **Reduced filters from 847 to 12** ✅ **50% faster debugging** ✅ **Business logic testable without WordPress**
**The Challenge:** *"We have a 12,000-line class with minified jQuery in the middle. Help."*

**BEFORE: The Stuff of Nightmares (True Story)**
```php
// *PTSD flashbacks intensify*
class Everything_Manager {
    public function do_everything($what = null, $how = null, $when = 'now', $why = 'because') {
        global $wpdb, $wp_query, $post, $current_user, $woocommerce; // All the globals!
        
        if ($what == 'user_stuff') {
            // 500 lines of user management
            $email_body = "<html><body>Welcome " . $_POST['name'] . "!</body></html>";
            wp_mail($_POST['email'], 'Welcome!', $email_body);
            
            $wpdb->insert($wpdb->users, ['user_login' => $_POST['username']]);
            
            // And yes, minified jQuery in the middle of PHP (I survived this)
            echo '<script>$(document).ready(function(){$("#thing").click(function(){$.post("ajax.php",{action:"thing"})});});</script>';
        }
        
        // ... 11,500 more lines of pure chaos
    }
}
```

**AFTER: Bounded Contexts Saved My Sanity**
```php
// Clean domains that actually make sense
namespace UserManagement\Domain {
    class User_Registration_Service {
        public function register(Registration_Request $request): Registration_Result {
            // Single responsibility: user registration only
        }
    }
}

namespace Billing\Domain {
    class Payment_Service {
        public function process_payment(Payment_Request $request): Payment_Result {
            // Single responsibility: payment processing only
        }
    }
}

// Application service coordinates domains
class User_Registration_Workflow {
    public function execute(Registration_Command $command): void {
        $user = $this->user_service->register($command->to_registration_request());
        
        if ($command->has_payment()) {
            $this->billing_service->process_payment($command->to_payment_request());
        }
        
        $this->email_service->send_welcome_email($user);
        
        // Event sourcing for complete audit trail
        $this->events->record(new User_Registered_Event($user->id));
    }
}
```

**Results:** ✅ Refactored at 2 AM ✅ Survived on coffee ✅ Still smiled by 8 AM

---

## **🔮 Career Highlights (Battle-Tested)**
- ⭐ **Codeable Expert:** Vetted to fix your worst plugin nightmares
- 🏆 **Envato Elite Author:** Released themes so sleek they nearly wrote themselves  
- 🚀 **Startup Founder at 21:** Managed 20+ devs (made mistakes so you don't have to)
- 🎤 **Cloudways Featured Developer:** [Official partner](https://www.cloudways.com/en/partners/codeable.php)
- 🧠 **LLM Optimization Specialist:** QLoRA & RAG tricks that slash AI costs

---

## **🛠 Tech Stack & Magic Wands**
- **Languages:** PHP · Python · JS/TS · SQL · React · LitElement  
- **Frameworks:** WordPress · FastAPI · Next.js · Celery · Redis · Docker  
- **Architecture:** DDD · CQRS · Event Sourcing · Clean Architecture  
- **AI & Data:** OpenAI · Claude · Grok · RAG pipelines · Hugging Face  

---

## **📢 Why Work With Me?**  
- **Your `functions.php` is a war crime.** I'll grant it amnesty.  
- **Your team debugs at 3 AM.** This stops now.  
- **Your codebase has PTSD.** I provide therapy (for code and developers).  
- **You need enterprise-grade architecture.** I deliver Michelin star code.

---

## **🎁 Choose Your Adventure!**  

### **1. "EMERGENCY! My Code Achieved Sentience!"** 
→ **Architecture Rescue Mission**  
*For when your codebase is one commit away from becoming self-aware*

### **2. "Build Something Michelin Star Grade"**  
→ **Enterprise WordPress Development**  
*Domain-driven design that would make Uncle Bob proud*

### **3. "Teach My Team to Stop Crying"**  
→ **Clean Architecture Mentoring**  
*Transform developers from WordPress cowboys to architecture astronauts*

---

## **📬 Let's Build Something Beautiful**  

[![Emergency Session](https://img.shields.io/badge/Emergency_Session-Save_My_Codebase-red?style=for-the-badge)](https://calendly.com/joseph-gabito/emergency)  
[![See My Work](https://img.shields.io/badge/GitHub-Michelin_Star_Code-black?style=for-the-badge&logo=github)](https://github.com/joseph-gabito)  
[![Codeable Profile](https://img.shields.io/badge/Codeable-Vetted_Expert-blue?style=for-the-badge)](https://codeable.io/developers/joseph-gabito/)

> *"WordPress isn't the problem. Your architecture is. Let's fix it."*  

**P.S.** I have a formal CS degree, so my approach is structured and based on tried-and-true patterns, not cargo cult programming.

**Warning:** Side effects may include pride in your codebase, the ability to deploy on Fridays, and code so clean it sparkles. ✨

---

<sub>**Credits:** Art by ChatGPT • Crafted with ☕, 16 years of battle scars, and an unhealthy obsession with bounded contexts</sub>
