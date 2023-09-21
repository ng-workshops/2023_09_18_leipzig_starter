# 14 Dynamic Modal

> npx ng generate component shared/modal --standalone=true

> create file src/app/shared/modal/modal.model.ts

> npx ng generate service shared/modal/modal

## shared/modal/modal.model.ts

```ts
type ModalTypes = 'basic' | 'warn' | 'primary';

export interface ModalData {
  title: string;
  message: string;
  type?: ModalTypes;
}
```

## shared/modal/modal.component.html

```html
<div
  class="closable backdrop"
  (click)="closeIt.emit()"
  (keyup.esc)="closeIt.emit()"
  role="none"
></div>

<div class="closable modal-dialog">
  <mat-card>
    <mat-card-title>{{ modal.title }}</mat-card-title>
    <mat-card-content>{{ modal.message }}</mat-card-content>
    <mat-card-content class="footer">
      <button (click)="cancel.emit()" mat-raised-button color="basic">
        Ähm No
      </button>
      <button (click)="closeIt.emit()" mat-raised-button [color]="modal.type">
        Oki doki
      </button>
    </mat-card-content>
  </mat-card>
</div>
```

## shared/modal/modal.component.ts

```ts
import { CommonModule } from '@angular/common';
import { Component, EventEmitter, Input, Output } from '@angular/core';
import { MatButtonModule } from '@angular/material/button';
import { MatCardModule } from '@angular/material/card';
import { ModalData } from './modal.model';

@Component({
  selector: 'app-modal',
  standalone: true,
  imports: [CommonModule],
  templateUrl: './modal.component.html',
  styleUrls: ['./modal.component.scss'],
})
export class ModalComponent implements OnInit {
  @Input({ required: true })
  modal!: ModalData;

  @Output()
  closeIt = new EventEmitter();

  @Output()
  cancel = new EventEmitter();
}
```

## shared/modal/modal.service.ts

```ts
import { Injectable, ViewContainerRef } from '@angular/core';
import { ModalComponent } from './modal.component';
import { ModalData } from './modal.model';

@Injectable({ providedIn: 'root' })
export class ModalService {
  open(data: ModalData, host: ViewContainerRef): ModalComponent {
    data.type = data.type || 'primary';
    return this.createModal(data, host);
  }

  private createModal(data: ModalData, host: ViewContainerRef): ModalComponent {
    host.clear();

    const modal = host.createComponent(ModalComponent);

    modal.instance.modal = data;
    modal.instance.closeIt.subscribe(() => modal.destroy());
    modal.instance.cancel.subscribe(() => modal.destroy());

    return modal.instance;
  }
}
```

## home/home.component.html

```html
<button (click)="openModal()">Open modal</button>
```

## home/home.component.ts

```ts
import { Component, ViewChild, ViewContainerRef, inject } from '@angular/core';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';
import { InfoBoxComponent } from './info-box/info-box.component';
import { MessageService } from './message.service';
import { InfoItemComponent } from './info-item/info-item.component';
import { ModalService } from '../shared/modal/modal.service';

@Component({
  selector: 'app-home',
  standalone: true,
  imports: [CommonModule, FormsModule, InfoBoxComponent, InfoItemComponent],
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.scss'],
})
export class HomeComponent {
  private modal = inject(ModalService);
  private hostElement = inject(ViewContainerRef);

  openModal() {
    const modal = this.modal.open(
      { message: this.customer.name, title: 'My name is', type: 'primary' },
      this.hostElement
    );

    modal.closeIt.subscribe(() => {
      console.log('MODAL closed');
    });

    modal.cancel.subscribe(() => {
      console.log('MODAL cancelled');
    });
  }
}
```
