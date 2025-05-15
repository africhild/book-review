# Proxy Design Pattern Summary (Instructor)

[Back to Book Home](../README.md)

Instructor's summary for the Proxy Design Pattern.

*   [Instructor Questions](./qa.md)
*   [sample-student Contributions](./contributors/sample-student/README.md)
*   [Proxy Pattern](./proxy/README.md) 

The Proxy pattern, on the other hand, involves creating a surrogate or placeholder object to control access to another object, which is useful for deferring object creation, managing remote objects, controlling permissions, or performing actions upon access, as shown with an image proxy in a document editor.


Types of Proxies: The sources describe several common applications of the Proxy pattern:

Remote Proxy: Represents an object residing in a different address space.

Virtual Proxy: Creates expensive objects on demand. The image proxy example falls into this category. It may cache information (like extent) to postpone accessing the real subject.

Protection Proxy: Controls access rights to the original object, checking if the caller has permissions.

Smart Reference: Replaces a bare pointer to perform additional actions upon object access, such as reference counting (for automatic deletion), loading persistent objects, or locking before access


Pros: The Proxy pattern introduces a level of indirection that is versatile. It allows for performance optimizations like creating expensive objects on demand (Virtual Proxy) or implementing copy-on-write. It can hide the complexity of accessing remote objects (Remote Proxy) or add security/logging before accessing the real object (Protection Proxy, Smart Reference).

Cons: The primary drawback is the introduction of indirection, which can potentially add a small overhead to method calls. Implementing proxies can be tedious, especially if manually forwarding many methods. Some implementation techniques, like C++ operator overloading, may not perfectly mimic a real object. Certain language features (like Smalltalk's doesNotUnderstand) might have limitations or performance issues



```ts
interface Graphics{
    draw(at: { x: number, y: number }): void
    getExtent(): {width: number, height: number }
}

class RealImage implements Graphics {
    private fileName: string
    private extent: { width: number, height: number }
    constructor(fileName: string) {
        this.fileName = fileName
        this.extent = { width: 100, height: 100 }
        this.loadImage()
    }
    private loadImage() {
        console.log(`Loading image: ${this.fileName}`)
        // Simulate loading time
        for (let i = 0; i < 1000000000; i++) {}
        console.log(`Image loaded: ${this.fileName}`)
    }
    draw(at: { x: number, y: number }) {
        console.log(`Drawing image: ${this.fileName} at (${at.x}, ${at.y})`)
    }
    getExtent() {
        return this.extent
    }
}
class ProxyImage implements Graphics {
    private realImage: RealImage | null = null
    private fileName: string;
    private cachedExtent: { width: number, height: number } | null = null; // Cache extent
    constructor(fileName: string) {
        this.fileName = fileName
    }

        // Helper to get the real image, creating it on demand
    protected getRealImage(): RealImage {
        if (this.realImage === null) {
            this.realImage = new RealImage(this.fileName);
        }
        return this.realImage;
    }
    
    draw(at: { x: number, y: number }) {
        this.getRealImage().draw(at);
    }
    getExtent() {
        if (this.cachedExtent === null) {
            this.cachedExtent = this.getRealImage().getExtent();
        }
        return this.cachedExtent;
    }

}
    // Client code using the proxy
console.log("--- Creating document with images ---");
const image1Proxy = new ProxyImage("photo1.jpg");
const image2Proxy = new ProxyImage("large_graph.png");

const images: Graphics[] = [image1Proxy, image2Proxy];
images.forEach((image, index) => {
    const extent = image.getExtent();
    console.log(`Image ${index + 1} extent: ${extent.width}x${extent.height}`);
    image.draw({ x: index * 10, y: index * 20 });
}
);
console.log("--- Assessment ---");
console.log("Note how 'Loading image...' messages only appeared when methods requiring the real object were called (getExtent, draw), not when the proxy was initially created.");
```