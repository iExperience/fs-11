# iXperience - Full Stack Day 11

Welcome to iXperience Full Stack 2019!

[[toc]]

## Auth Guards 

### Add Auth Guard Service

Add Auth Guard service to existing Ionic / Angular application

```bash

ionic generate service guard/auth-guard

```

Update the code within the service, ensure the the service class implements interface CanActivate. Read a boolean value from the AuthenticationService to see if the user is logged in. This will determine whether or not the user can activate certain routes.

```ts

import { Injectable } from '@angular/core';
import { CanActivate, ActivatedRouteSnapshot } from '@angular/router';
import { AuthenticationService } from '../services/auth.service';
import { AlertController } from '@ionic/angular';

@Injectable({
  providedIn: 'root'
})
export class AuthGuardService implements CanActivate {

  constructor(private authenticationService: AuthenticationService, private alertController: AlertController) { }

  canActivate(private activatedRouteSnapshot: ActivatedRouteSnapshot) {
    if (this.authenticationService.getLoggedInStatus()) {
      return true;
    }
    else {
      this.alertController.create({
        header: "Sorry",
        subHeader: "You are unable to access this path",
        message: "Please login or register",
        buttons: ["OK"]
      }).then(alert => alert.present());
      return false;
    }
  }
  
}

```

### Update the Angular Router Module

Update the app-routing-module.ts file to use the AuthGuardService to verify route permissions. 

```ts

import { NgModule } from '@angular/core';
import { PreloadAllModules, RouterModule, Routes } from '@angular/router';
import { AuthGuardService } from './guards/auth-guard.service';

const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' },
  { path: 'home', loadChildren: './pages/home/home.module#HomePageModule' },
  { path: 'listings', canActivate: [AuthGuardService], loadChildren: './pages/listings/listings.module#ListingsPageModule' },
  { path: 'users', canActivate: [AuthGuardService], loadChildren: './pages/users/users.module#UsersPageModule' },
  { path: 'register', loadChildren: './pages/register/register.module#RegisterPageModule' },
  { path: 'update-user', canActivate: [AuthGuardService], loadChildren: './pages/update-user/update-user.module#UpdateUserPageModule' },
];

@NgModule({
  imports: [
    RouterModule.forRoot(routes, { preloadingStrategy: PreloadAllModules })
  ],
  exports: [RouterModule]
})
export class AppRoutingModule { }

```

Test to see if a user can activate various routes, when logged in or not.