---
title: 'Understanding Push Notifications in WhatsApp: Architecture, Flow, and Behavior'
seoTitle: 'Push Notifications in WhatsApp: Architecture, Flow, and Behavior'
date: 2024-12-18T18:35:38+05:30
description: Learn how WhatsApp push notifications work, including their architecture, connection with FCM/APNs, and message handling flow on your device.
slug: push-notifications-whatsapp-architecture-flow
categories:
  - System Design
---
## Understanding Push Notifications: Architecture and Flow

Push notifications are a core feature in modern apps, enabling real-time communication with users. Whether it’s a messaging app like WhatsApp or a social media platform, the process involves multiple components working together seamlessly. Let’s break it down step by step.

## Push Notification Architecture

The architecture of push notifications is built around three key players:

1. **Mobile App**: The app installed on the user’s device that wants to notify the user about certain events.

2. **Push Notification Services**: These services act as intermediaries between the app’s server and the user’s device. Common examples are:
	* **Google Firebase Cloud Messaging (FCM)**: For Android and web platforms.
	* **Apple Push Notification Service (APNs)**: For iOS devices.

3. **Backend Server**: The server where the app’s logic resides, responsible for sending notifications to the push notification service.

## Persistent Connections with Push Services

A critical aspect of push notifications is the persistent connection between the device and the push service. When you install an app like WhatsApp, the app registers itself with Google FCM or Apple APNs. These services maintain a lightweight, always-on connection with the user’s device.

The operating system (Android or iOS) ensures that the connection is efficient, preserving battery life and minimizing resource use. This allows the push notification service to deliver notifications to the device at any time without requiring the app itself to maintain a direct connection.

## Push Notification Flow

Now, let’s look at how a push notification, such as a message on WhatsApp, flows from the sender to the recipient’s device.

1. **WhatsApp Server Sends a Push Notification**
	
	When someone sends a message, the WhatsApp server prepares a notification for the recipient. This notification contains metadata such as:
	* The sender’s ID
	* A reference to the message (not the actual content, as it’s encrypted)
	* Other details like the message timestamp.
	The server then forwards this notification to Google FCM (for Android users) or Apple APNs (for iOS users).

2. **Google FCM or Apple APNs Delivers the Notification**
	
	Once the push notification service (FCM/APNs) receives the request from the WhatsApp server, it delivers the notification to the recipient’s device. This step involves the persistent connection maintained between the push service and the device.

3. **Device Handles the Notification**

	When the notification arrives on the device, the behavior depends on the state of the app:

	* **If the app is closed:**

		The operating system displays the notification directly (e.g., “New message received”) without the app doing anything. The actual message content is not downloaded until the user opens WhatsApp.

	* **If the app is in the background:**

		The notification is sent to WhatsApp in the background. WhatsApp fetches the message from its server, decrypts it, and saves it in the local database. Once processed, WhatsApp updates the notification with detailed information such as the sender’s name and a message preview (e.g., “John: Hi, how are you?”).

	* **If the app is in the foreground:**

		The notification bypasses the system notification tray and goes directly to WhatsApp. The app processes the message in real time and updates the user interface.

## Key Behavior of WhatsApp Notifications

A common misconception is that WhatsApp sends different types of notifications (e.g., “data” or “notification” messages). In reality, the WhatsApp server sends a single notification containing metadata, and the device’s state determines how the notification is handled. For instance:

* When the app is closed, the OS displays the notification.
* When the app is in the background, WhatsApp processes the notification and fetches the message.
* When the app is open, WhatsApp handles everything directly without showing a system notification.

## Conclusion

Push notifications are designed to efficiently deliver information to users, whether the app is open, closed, or running in the background. The persistent connection between devices and push services like Google FCM or Apple APNs ensures reliability without draining resources. In apps like WhatsApp, this system works seamlessly to fetch, decrypt, and store messages while providing users with timely notifications.

This architecture and flow make push notifications a powerful tool for modern app developers, balancing efficiency, security, and user experience.